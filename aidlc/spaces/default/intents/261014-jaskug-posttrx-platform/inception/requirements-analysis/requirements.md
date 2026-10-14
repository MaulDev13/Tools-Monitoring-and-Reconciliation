# Dokumen Requirement — Platform Post-Transaction Jasa Keuangan (Jaskug)

> Versi analisis requirement berbasis BRD "Tools Monitoring, Rekonsiliasi dan Settlement Transaksi Jasa Keuangan" (PT Pos Indonesia, v1.0.0) dan jawaban klarifikasi Q1–Q10. Platform dibangun sebagai satu produk end-to-end yang menyatukan empat tools di atas dua fondasi bersama.

## 1. Ringkasan & Prinsip

- **Single Source of Truth:** data internal PT Pos Indonesia adalah acuan mutlak untuk replikasi, rekonsiliasi, dan settlement.
- **Satu platform end-to-end:** Monitoring → Rekonsiliasi → Kalkulasi & Nota Settlement → Settlement & Monitoring, di atas fondasi RBAC + Konfigurasi Parameter.
- **Seluruh skema rekonsiliasi tercakup** (tidak dibatasi "Pos generate saja"), termasuk rekonsiliasi berbasis data mitra dan portal mitra.
- **Di luar lingkup:** eksekusi/transfer pembayaran dana ke mitra (sistem berhenti di Nota Settlement + data pembayaran).
- **Integrasi:** hanya sistem internal Pos (SOPP, SAP, Remittance, CGS, CRM), di-mock dulu via kontrak antarmuka yang jelas.

Notasi: `FR-x.y` = functional requirement; AC = Acceptance Criteria; jejak ke BRD lewat FU/BO/SC.

---

## 2. Fondasi F1 — Autentikasi & RBAC (FU-1 / BO-1 / SC-1)

**FR-1.1 Autentikasi pengguna.** Sistem menyediakan login berbasis kredensial (username/email + kata sandi), menolak kredensial tidak valid dengan pesan kesalahan, dan membuat sesi aman saat berhasil.
- AC: 100% keberhasilan login & otorisasi untuk setiap role tanpa kegagalan sistem [SC-1]; kredensial salah ditolak dengan pesan jelas; sesi kedaluwarsa sesuai kebijakan.

**FR-1.2 Otorisasi berbasis peran (RBAC).** Sistem mengidentifikasi peran saat login dan hanya menampilkan menu/fitur sesuai hak akses peran tersebut, untuk 10 role (Super Admin, Admin, Helpdesk, Petugas Rekonsiliasi, Asmen Rekonsiliasi, Petugas Adm, Manajer Adm, VP, Petugas Settlement, OCFS).
- AC: tiap role hanya mengakses fitur dalam kewenangannya; akses di luar kewenangan ditolak dan tercatat.

**FR-1.3 Manajemen akun & mapping hak akses.** Super Admin mengelola akun pengguna dan memetakan hak akses per peran.
- AC: pembuatan/penonaktifan akun dan perubahan mapping berlaku efektif pada sesi berikutnya; semua perubahan tercatat pada audit trail.

---

## 3. Fondasi F2 — Konfigurasi Parameter Kontrak Mitra (FU-2 / BO-2 / SC-2)

**FR-2.1 Master data kontrak & kemitraan.** Admin menetapkan nama produk/layanan, nama mitra, periode PKS (auto non-aktif saat kedaluwarsa), ketentuan pembatalan transaksi, tipe kemitraan (Direct/Agregator/Switcher), entitas agregator/switcher, dan komponen biaya aktif. Data produk & mitra ditarik otomatis dari sistem internal Pos.
- AC: penetapan skema untuk ≥5 produk berbeda berhasil 100% dan dipakai sebagai parameter alur [SC-2]; produk otomatis non-aktif saat PKS berakhir.

**FR-2.2 Matriks skema perhitungan & multi-tarif.** Mendukung fix flat/persen, tiering/kelipatan nominal (MPN3/MTF/Samsat), matriks multi-tarif kanal B2B/Modern Channel, dan breakdown komponen biaya per kanal (Loket/Mobile/Agen/B2B) termasuk pengaturan resi, mekanisme pembayaran, frekuensi invoice, PPN, pihak settle, dan messaging.
- AC: parameter tersimpan dan konsisten dipakai engine kalkulasi (lihat FR-12).

**FR-2.3 Multi-Cut Off Time (COT) & SLA Auto-Approve.** Admin menetapkan batch COT (awal/akhir), periode rekon & settle (H+0/H+1), jam settle, dan SLA Auto-Approve per batch.
- AC: scheduler replikasi (FR-4) mengeksekusi sesuai COT yang dikonfigurasi.

**FR-2.4 Konfigurasi pihak settlement & rekening CGS.** Menetapkan jumlah pihak settlement (1–3), PKP/faktur, bank & rekening mitra, kode GL/COA SAP, dan nomor rekening penampungan CGS.
- AC: data rekening & GL dipakai pada pembentukan jurnal (FR-15).

**FR-2.5 Konfigurasi pajak dinamis.** Per komponen biaya: jenis pajak (PPN, PPh 23, atau tidak kena pajak), tarif, DPP (termasuk input formula), dan trigger integrasi.
- AC: parameter pajak dipakai engine kalkulasi pajak (FR-12) dengan hasil akurat 100% [SC-12].

**FR-2.6 Generator file & dokumen.** Pengaturan prefix nama file, format tanggal, delimiter, ekstensi output rekon (.txt/.a/.b/.c), serta pilihan & format dokumen finansial (Nota Settlement, rekap bulanan; PDF/Excel).
- AC: output file & dokumen mengikuti konfigurasi.

**FR-2.7 Dynamic field builder & parameter rekonsiliasi.** Skema rekonsiliasi (1–4), format rekon agregator, sumber data (API/SFTP), data yang diakui, toleransi nominal & waktu, serta pembangun field rekon (key, tipe data, format, prefix/suffix).
- AC: parameter dipakai engine rekonsiliasi (FR-5) sebagai aturan pencocokan.

---

## 4. Tool 1 — Monitoring Transaksi Real-time (FU-3 / BO-3 / SC-3)

**FR-3.1 Dashboard & pencarian transaksi real-time.** Dashboard summary hari berjalan (total trx, total nominal, jumlah per status), Quick Search (no referensi, transaction ID), Advanced Search (periode, produk, kantor pos, status), tampil hanya setelah pencarian/filter, dengan sorting, filter, refresh manual/otomatis, dan ringkasan jumlah + total nominal.
- AC: akurasi pencarian ≥99% pada ≥10 skenario Helpdesk [SC-3].

**FR-3.2 Pembatalan transaksi.** Validasi kelayakan (ketentuan pembatalan produk, pencegahan pembatalan produk purchase, kewenangan), input No. BA UPT + mandatory remarks. Transaksi < Rp10.000.000 diproses langsung; transaksi di atas ambang melalui approval Manajer Helpdesk (approve → status dibatalkan; reject → proses dihentikan).
- AC: keberhasilan validasi & proses pembatalan sesuai business rule 100% [SC-3]; seluruh alur approval berjalan sesuai ketentuan; perlakuan nominal tepat Rp10.000.000 mengikuti keputusan di `open-questions.md`.

**FR-3.3 Push payment transaksi timeout.** Validasi kelayakan (status SOPP, status mitra, jenis produk), cegah push atas transaksi yang sudah sukses/dibatalkan/direfund/kedaluwarsa, tampilkan detail transaksi + konfirmasi, re-push ke mitra, proses respons (sukses → Success; gagal → status sesuai respons + alasan), dan catat audit trail request/response.
- AC: push payment hanya untuk transaksi yang layak; seluruh aktivitas tercatat lengkap pada audit trail.

---

## 5. Tool 2 — Rekonsiliasi (FU-4 s.d. FU-11 / BO-4 s.d. BO-11)

**FR-4 Replikasi, cleansing & draft LHR (FU-4 / BO-4 / SC-4).**
- FR-4.1 Scheduler ganda: Multi-COT (H+0, per batch) dan EOD (H-1, 00.01–01.00), eliminasi transaksi gagal, data internal sebagai acuan tunggal.
- FR-4.2 Pengelompokan per produk/tanggal, penyimpanan sebagai bahan rekonsiliasi & settlement, tampil ke pengguna, unduh Excel.
- FR-4.3 Deteksi transaksi double (produk, no pelanggan, nominal, periode tagihan); disimpan terpisah, ditampilkan sebagai informasi, dapat diunduh.
- FR-4.4 Generate draft LHR otomatis (termasuk data double sebagai bahan sebelum verifikasi).
- AC: otomatisasi generate EOD 100% selama ≥14 hari kerja berturut; data & draft LHR lengkap, akurat, sesuai format [SC-4].

**FR-5 Rekonsiliasi oleh sistem — data mitra (FU-5 / BO-5 / SC-5).**
- FR-5.1 Penyediaan data mitra: unggah Excel/CSV atau tarik via API/integrasi; validasi struktur file; info jumlah data diterima; simpan; dukung unggah ulang.
- FR-5.2 Ambil konfigurasi parameter rekonsiliasi mitra (field kriteria, data yang diakui, toleransi nominal & waktu).
- FR-5.3 Proses pencocokan otomatis → klasifikasi matched/forced/suspect; simpan jumlah & total nominal tiap kelas + rincian.
- FR-5.4 Penyajian hasil + unduh (Excel/CSV/PDF).
- FR-5.5 Dashboard monitoring progres rekonsiliasi seluruh produk (status: Belum Direkonsiliasi → Selesai), persentase progres, detail, filter, pencarian, drill-down.
- AC: akurasi pencocokan ≥99% dan klasifikasi 100% pada ≥10 dataset mitra; laporan dihasilkan & diunduh sesuai format [SC-5].

**FR-6 Tampilan data untuk petugas rekonsiliasi (FU-6 / BO-6 / SC-6).** Pilih parameter (tanggal, produk); tampil tabel terstruktur + ringkasan (jumlah trx, total nominal, total biaya admin); rekap per kategori biaya admin; pencarian multi-kata kunci; deteksi & penanganan transaksi double; tampilan draft LHR.
- AC: akurasi tampilan ≥99% dan identifikasi data identik 100% pada ≥10 dataset [SC-6].

**FR-7 Referensi SOPP-3A (FU-7 / BO-7 / SC-7).** Tarik SOPP-3A otomatis dari sistem SOPP (periode H-1), tampilkan sebagai pembanding saat verifikasi/validasi, tanpa memengaruhi penentuan kebenaran data.
- AC: akurasi data ≥99% pada ≥7 hari data historis SOPP-3A [SC-7].

**FR-8 Verifikasi & validasi berjenjang (FU-8 / BO-8 / SC-8).** Persetujuan tingkat 1 (petugas rekonsiliasi) dan validasi akhir (asisten manajer) → data & LHR terkunci; audit trail setiap aktivitas; perbaikan via mekanisme pembatalan/inject (FR-9) + generate ulang draft bila ditemukan anomali/double.
- AC: keberhasilan alur persetujuan 100% pada ≥20 set data; audit trail lengkap [SC-8]; hubungan "tanpa penolakan" vs perbaikan diklarifikasi di `open-questions.md`.

**FR-9 Data cancellation, refund & inject (FU-9 / BO-9 / SC-9).**
- FR-9.1 Identifikasi kandidat (force/suspect/double) + status di SOPP & DS Client; pencarian/penyaringan.
- FR-9.2 Pelaksanaan pembatalan di SOPP dan/atau DS Client; pencatatan pembatalan sebagai transaksi (tanggal, id asal & pembatalan, nopend); histori/log.
- FR-9.3 Pengelolaan refund: Modern Channel → rekening Pospay; Loket → cek CRM Remittance, bila unregistered ditangguhkan (eskalasi ticketing), bila tervalidasi generate VA + KYC di CGS lalu transfer ke VA; penginformasian & pencairan mengikuti mekanisme as-is.
- FR-9.4 Feeder jurnal koreksi/pembalik ke SAP saat EOD; pencatatan pembatalan pada SOPP1–SOPP6.
- FR-9.5 Inject data suspect sebagai transaksi hari berjalan (tanggal inject, bukan H-1), validasi anti-duplikat, simpan referensi asal (traceability) + histori; transaksi inject mengikuti alur operasional normal.
- FR-9.6 Catatan Rekon Manual (tabel cancellation & flagging/inject) menjadi bagian LHR, dengan total nominal otomatis.
- AC: keberhasilan koreksi 100% dan akurasi pencatatan 100% tanpa selisih pada ≥10 skenario [SC-9].

**FR-10 Distribusi LHR multi-kanal (FU-10 / BO-10 / SC-10).** Konfigurasi metode distribusi per mitra (Email/Telegram/FTP-SFTP/tidak ada), jadwal & format; pengiriman via Email, FTP/SFTP, Telegram (dengan status, log, retry); portal akses mandiri mitra (daftar/filter/unduh); monitoring status distribusi (Waiting/Sent/Failed) + pencarian/filter + unduh data distribusi.
- AC: delivery rate 100% pada ≥10 skenario konfigurasi mitra; pencatatan pengiriman lengkap [SC-10].

**FR-11 Portal rekonsiliasi mandiri mitra (FU-11 / BO-11 / SC-11).** Mitra unggah data (Excel/CSV) dengan validasi struktur; setting parameter rekonsiliasi mandiri (field kriteria, toleransi); proses pencocokan otomatis → matched/forced/suspect; penyajian + unduh (Excel/CSV/PDF); dashboard monitoring rekonsiliasi mitra.
- AC: portal melayani login eksternal, visibilitas data, dan unduh laporan dengan uptime 99,9% [SC-11].

---

## 6. Tool 3 — Kalkulasi & Nota Settlement (FU-12, FU-13 / BO-12, BO-13 / SC-12, SC-13)

**FR-12 Engine kalkulasi finansial (FU-12 / BO-12 / SC-12).**
- FR-12.1 Ambil data LHR tervalidasi (status matched/reconciled, per periode & mitra), kunci data yang dipakai.
- FR-12.2 Ambil komponen biaya dari parameter (FR-2.2); engine kalkulasi fee: flat, persen, tiering, multi-tarif B2B; skema pembayaran langsung/invoice.
- FR-12.3 Ambil parameter pajak (FR-2.5); hitung PPN & PPh 23 untuk skema fee exclude PPN dan include PPN (sesuai contoh BRD).
- FR-12.4 Hitung Net Settlement **per komponen biaya aktif** (Biaya Admin, Biaya Jaringan, Sharing Fee, dll) berdasarkan skema pembayaran tiap komponen di parameter FU-2 (flat/persen, exclude/include PPN, mekanisme pembayaran langsung/invoice, pihak settle), lalu akumulasikan menjadi Net Settlement. Tidak ada formula tunggal tetap; angka contoh BRD hal. 62 adalah ilustrasi satu skema. Tampilkan formula & rincian per komponen; simpan hasil. (Lihat OQ-1.)
- FR-12.5 Bentuk draft Nota Settlement (nomor unik, PDF; identitas perusahaan & mitra, periode, referensi rekonsiliasi, rincian transaksi/fee/pajak, Net Settlement).
- AC: akurasi 100% pada ≥20 sampel variasi kontrak mitra tanpa intervensi manual [SC-12]; simulasi angka net settlement mengikuti perbaikan di `open-questions.md`.

**FR-13 Workflow approval Nota Settlement — Maker-Checker-Approver (FU-13 / BO-13 / SC-13).**
- FR-13.1 Tahap 1 (Petugas Administrasi, Maker): cek breakdown (gross, fee, pajak, net); keputusan Approve / Revision Required; catatan; catat identitas & waktu.
- FR-13.2 Tahap 2 (Manajer Administrasi, Checker): pemeriksaan & keputusan serupa; teruskan ke approver bila disetujui.
- FR-13.3 Tahap 3 (VP, Approver): Final Approve / Revision Required; set status Approved; izinkan proses ke pembayaran; catat identitas & waktu.
- FR-13.4 Monitoring workflow approval (status, posisi dokumen, Maker/Checker/Approver, waktu, pencarian, dashboard).
- FR-13.5 Revisi via ubah parameter + generate ulang Nota (pengubahan langsung dilarang, anti-fraud); kirim kembali ke alur persetujuan awal.
- FR-13.6 SLA Auto-Approve (**diizinkan**, OQ-5): sistem membaca parameter SLA Auto-Approve (Jam) dari FU-2; bila otorisator tidak memutuskan sampai lewat jam SLA, status Auto-Approve dieksekusi agar siklus pelimpahan ke mitra lancar. Setiap Auto-Approve tercatat pada audit trail (status "Auto-Approved", tahap, waktu, parameter SLA).
- AC: dokumen terkunci otomatis pasca persetujuan akhir; audit trail digital 100% di tiap tahap, termasuk setiap kejadian Auto-Approve [SC-13].

---

## 7. Tool 4 — Settlement & Monitoring Settlement (FU-14, FU-15, FU-16 / BO-14, BO-15, BO-16 / SC-14, SC-15, SC-16)

**FR-14 Dashboard monitoring settlement (FU-14 / BO-14 / SC-14).** Visual status real-time (Draft, Waiting for Checker, Waiting for Approval, Revision Required, Final Approved, Paid); filter (mitra, rentang tanggal, no dokumen, status); unduh rekap settlement (Excel/CSV); histori pergerakan dokumen lengkap dengan waktu & identitas.
- AC: akurasi sinkronisasi data 100% antara status back-end dan tampilan dashboard [SC-14].

**FR-15 Integrasi penjurnalan SAP (FU-15 / BO-15 / SC-15).** Input sumber rekening bank Pos (Petugas Settlement); ambil hasil fee/pajak/net + sumber bank ke GL per mitra; posting jurnal Host-to-Host via API ke SAP secara real-time pasca Final Approved; mekanisme auto-retry (otomatis/manual) + tampilan kendala informatif.
- AC: rasio sukses 100% pembentukan nomor jurnal di SAP pasca Final Approved tanpa timeout/mismatch nominal [SC-15].

**FR-16 Rekapitulasi bulanan Pospay (FU-16 / BO-16 / SC-16).** Ambil Nota Settlement berstatus Final Approved/Paid per periode bulan & mitra; rekap harian (akumulasi TRX, BSU, fee, DPP, PPN, PPh 23, settlement) + total akhir periode; penyajian + pencarian (mitra, periode) + cetak/ekspor PDF dengan tanggal/waktu cetak.
- AC: akurasi 100% pada penarikan data harian, kalkulasi akumulasi, dan ekspor dokumen tanpa anomali [SC-16].

---

## 8. Requirement Non-Fungsional (NFR)

| Kode | NFR | Deskripsi | Jejak |
|---|---|---|---|
| NFR-1 | Keamanan & kerahasiaan | Kontrol akses RBAC, perlindungan data RAHASIA, pengamanan jalur distribusi & portal eksternal | RB-8, FU-1, FU-10/11 |
| NFR-2 | Auditability | Jejak audit lengkap untuk login, pembatalan, push payment, verifikasi, approval, distribusi, jurnal | SC-8, SC-13, FU-9/10 |
| NFR-3 | Kinerja & skalabilitas | Tahan volume transaksi besar; proses EOD & tampilan data tanpa lag/timeout | RB-5 |
| NFR-4 | Keandalan integrasi | Auto-retry & penanganan error untuk SAP; penanganan kegagalan SOPP/Remittance/CGS/CRM | RB-2, RB-7, RB-9 |
| NFR-5 | Ketersediaan portal mitra | Uptime 99,9% | SC-11 |
| NFR-6 | Akurasi finansial | Tidak boleh ada selisih pada fee/pajak/net settlement | RB-6, SC-12 |
| NFR-7 | Keterbacaan & lokalisasi | Antarmuka & dokumen dalam Bahasa Indonesia | — |

## 9. Integrasi Eksternal (semua internal Pos, mock-first)

| Sistem | Fungsi | Dipakai di | Strategi awal |
|---|---|---|---|
| SOPP | Sumber transaksi; status payment; data SOPP-3A; pembukuan SOPP1–6 | FR-3.3, FR-4, FR-7, FR-9 | Mock/stub via kontrak antarmuka; siap diganti API nyata |
| SAP | Posting jurnal keuangan & jurnal koreksi | FR-9.4, FR-15 | Mock/stub |
| Remittance | Orkestrasi refund | FR-9.3 | Mock/stub |
| CGS (Core Giro System) | Generate VA, rekening penampungan | FR-2.4, FR-9.3 | Mock/stub |
| CRM | Validasi data pelanggan (refund Loket) | FR-9.3 | Mock/stub |

> Catatan: setiap integrasi dirancang di belakang antarmuka (port/adapter) sehingga mock dapat ditukar dengan API nyata tanpa mengubah logika inti saat API tersedia.

## 10. Di Luar Lingkup

- Eksekusi/transfer pembayaran dana ke mitra (sistem hanya menghasilkan Nota Settlement + data pembayaran).
- Perubahan/koreksi/penghapusan data langsung pada sistem sumber (koreksi via mekanisme pembatalan/inject pada platform).
- Pengelolaan infrastruktur komunikasi di sisi penerima mitra.

## 11. Jejak Ringkas FU → FR

| BRD FU | Requirement | BRD BO | BRD SC |
|---|---|---|---|
| FU-1 | FR-1.1–1.3 | BO-1 | SC-1 |
| FU-2 | FR-2.1–2.7 | BO-2 | SC-2 |
| FU-3 | FR-3.1–3.3 | BO-3 | SC-3 |
| FU-4 | FR-4.1–4.4 | BO-4 | SC-4 |
| FU-5 | FR-5.1–5.5 | BO-5 | SC-5 |
| FU-6 | FR-6 | BO-6 | SC-6 |
| FU-7 | FR-7 | BO-7 | SC-7 |
| FU-8 | FR-8 | BO-8 | SC-8 |
| FU-9 | FR-9.1–9.6 | BO-9 | SC-9 |
| FU-10 | FR-10 | BO-10 | SC-10 |
| FU-11 | FR-11 | BO-11 | SC-11 |
| FU-12 | FR-12.1–12.5 | BO-12 | SC-12 |
| FU-13 | FR-13.1–13.6 | BO-13 | SC-13 |
| FU-14 | FR-14 | BO-14 | SC-14 |
| FU-15 | FR-15 | BO-15 | SC-15 |
| FU-16 | FR-16 | BO-16 | SC-16 |

## Assumptions & Open Questions

Lihat `open-questions.md` untuk daftar inkonsistensi BRD yang perlu konfirmasi tim bisnis sebelum desain detail.
