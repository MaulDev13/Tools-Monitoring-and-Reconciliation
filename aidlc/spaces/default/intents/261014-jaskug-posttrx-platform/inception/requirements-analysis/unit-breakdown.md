# Pembagian Unit (Increment) — Platform Post-Transaction Jaskug

> Platform dipecah menjadi unit-unit kecil yang dapat dibangun dan diuji terpisah, mengikuti ketergantungan data: fondasi dulu, lalu per-tool. Setiap unit menyebut requirement (FR) yang dicakup dan dependensinya.

## Prinsip Pembagian

- **Fondasi lebih dulu.** FU-1 (RBAC) dan FU-2 (parameter) adalah pijakan semua tool; FU-2 adalah "otak" sistem (menentukan cara rekonsiliasi, kalkulasi, settlement).
- **Satu produk end-to-end**, tetapi dibangun increment demi increment.
- **Integrasi eksternal mock-first** (SOPP/SAP/Remittance/CGS/CRM) di belakang antarmuka port/adapter.
- Urutan mengikuti aliran data: Monitoring → Rekonsiliasi → Kalkulasi/Nota → Settlement.

---

## Modul 0 — Fondasi

| Unit | Nama | Mencakup FR | Dependensi |
|---|---|---|---|
| U0A | Kerangka aplikasi & autentikasi | FR-1.1 | — |
| U0B | RBAC: peran, akun, mapping hak akses | FR-1.2, FR-1.3 | U0A |
| U0C | Master data mitra & produk (read-only dari sistem internal) | FR-2.1 (sumber data) | U0A; mock sistem internal |
| U0D | Konfigurasi parameter: master kontrak & kemitraan | FR-2.1 | U0B, U0C |
| U0E | Konfigurasi parameter: skema perhitungan & pajak | FR-2.2, FR-2.5 | U0D |
| U0F | Konfigurasi parameter: COT/SLA, pihak settlement/CGS, generator, dynamic field | FR-2.3, FR-2.4, FR-2.6, FR-2.7 | U0D |

## Modul 1 — Tool Monitoring Transaksi Real-time

| Unit | Nama | Mencakup FR | Dependensi |
|---|---|---|---|
| U1A | Dashboard summary & pencarian transaksi | FR-3.1 | U0B; mock SOPP (data transaksi) |
| U1B | Pembatalan transaksi + approval ambang Rp10 juta | FR-3.2 | U1A, U0E (ketentuan pembatalan) |
| U1C | Push payment transaksi timeout | FR-3.3 | U1A; mock SOPP & mitra |

## Modul 2 — Tool Rekonsiliasi

| Unit | Nama | Mencakup FR | Dependensi |
|---|---|---|---|
| U2A | Replikasi & cleansing (scheduler Multi-COT & EOD) | FR-4.1, FR-4.2 | U0F (COT), mock SOPP |
| U2B | Deteksi transaksi double | FR-4.3 | U2A |
| U2C | Generate draft LHR otomatis | FR-4.4 | U2A, U2B, U0E |
| U2D | Referensi SOPP-3A | FR-7 | mock SOPP |
| U2E | Tampilan data untuk petugas rekonsiliasi | FR-6 | U2A, U2C |
| U2F | Verifikasi & validasi berjenjang (lock LHR) | FR-8 | U2E, U2D, U0B |
| U2G | Data cancellation + feeder jurnal koreksi | FR-9.1, FR-9.2, FR-9.4 | U2F; mock SOPP & SAP |
| U2H | Orkestrasi refund (Modern Channel & Loket) | FR-9.3 | U2G; mock Remittance/CGS/CRM |
| U2I | Inject data + Catatan Rekon Manual | FR-9.5, FR-9.6 | U2F, U2C |
| U2J | Distribusi LHR multi-kanal + monitoring distribusi | FR-10 | U2F; mock Email/FTP/Telegram |
| U2K | Rekonsiliasi berbasis data mitra (upload/API, matching, dashboard progres) | FR-5.1–5.5 | U0F, U2A |
| U2L | Portal rekonsiliasi mandiri mitra | FR-11 | U0B, U2K |

## Modul 3 — Tool Kalkulasi & Nota Settlement

| Unit | Nama | Mencakup FR | Dependensi |
|---|---|---|---|
| U3A | Engine kalkulasi fee | FR-12.1, FR-12.2 | U2F (LHR terkunci), U0E |
| U3B | Engine kalkulasi pajak (PPN/PPh 23, exclude/include) | FR-12.3 | U3A, U0E |
| U3C | Net settlement & draft Nota Settlement (PDF) | FR-12.4, FR-12.5 | U3A, U3B, U0F |
| U3D | Workflow approval Maker-Checker-Approver | FR-13.1–13.3 | U3C, U0B |
| U3E | Monitoring approval, revisi, SLA Auto-Approve | FR-13.4–13.6 | U3D |

## Modul 4 — Tool Settlement & Monitoring Settlement

| Unit | Nama | Mencakup FR | Dependensi |
|---|---|---|---|
| U4A | Dashboard monitoring settlement (Draft→Paid) | FR-14 | U3D |
| U4B | Input sumber bank + integrasi jurnal SAP (Host-to-Host, auto-retry) | FR-15 | U3D, U0F; mock SAP |
| U4C | Rekapitulasi bulanan Pospay (cetak/ekspor PDF) | FR-16 | U3C/U3D, U4A |

## Modul X — Pondasi Lintas (dibangun bertahap bersama modul terkait)

| Unit | Nama | Mencakup | Dependensi |
|---|---|---|---|
| UX1 | Lapisan adapter integrasi (port + mock) SOPP/SAP/Remittance/CGS/CRM | NFR-4, bagian integrasi | awal, dipakai banyak unit |
| UX2 | Audit trail & logging lintas fitur | NFR-2 | awal |
| UX3 | Fondasi non-fungsional (keamanan, kinerja) | NFR-1, NFR-3, NFR-6 | awal |

## Urutan Pengembangan yang Disarankan

1. **UX1–UX3** (adapter mock, audit, keamanan) + **U0A → U0F** — fondasi.
2. **U1A → U1C** — Monitoring (paling independen, cepat memberi nilai).
3. **U2A → U2F** — jalur inti rekonsiliasi (replikasi → double → draft LHR → SOPP-3A → tampilan → verifikasi).
4. **U2G → U2I** (koreksi: cancellation, refund, inject) lalu **U2J** (distribusi).
5. **U2K → U2L** — rekonsiliasi data mitra & portal mitra.
6. **U3A → U3E** — kalkulasi & Nota Settlement + approval MCA.
7. **U4A → U4C** — settlement, jurnal SAP, rekap bulanan.

> Catatan walking skeleton: irisan end-to-end tertipis yang bermakna adalah U0A+U0B (login+RBAC) → U1A (lihat transaksi) sebagai bukti integrasi awal, sebelum membangun jalur rekonsiliasi penuh.
