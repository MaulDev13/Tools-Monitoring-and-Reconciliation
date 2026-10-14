# Stakeholder Map — Platform Post-Transaction Jasa Keuangan (Jaskug)

> Pemetaan pemangku kepentingan untuk platform post-transaction Jaskug. Setiap baris mencantumkan sumber ([Q<n>] = jawaban klarifikasi, [desc] = deskripsi awal, BRD = dokumen BRD).

## Pemangku Kepentingan Kunci dan Kepentingannya

| Stakeholder | Jenis | Kepentingan Utama | Source |
|---|---|---|---|
| Unit Operasi Jasa Keuangan | Internal — pemilik proses bisnis | Akurasi & integritas rekonsiliasi dan settlement; penyelesaian operasional cepat dan terdokumentasi | [Q5: E] |
| Sub Direktorat Digital Services | Internal — pemilik sistem & pengembangan | Pengembangan sistem sesuai kebutuhan; keamanan; keberlanjutan platform | [Q5: E] |
| Tim Keuangan / SAP & unit perpajakan | Internal — integritas keuangan | Ketepatan penjurnalan ke SAP; kebenaran perhitungan fee, PPN, PPh 23 | [Q5: E] |
| Mitra eksternal jasa keuangan | Eksternal — pengguna sekunder | Transparansi data; akses & unduh Laporan Hasil Rekonsiliasi; rekonsiliasi mandiri | [Q2: C], [Q5: E] |

## Peran Pengguna Sistem (Internal)

| Role | Peran dalam Platform | Source |
|---|---|---|
| Super Admin | Manajemen akun pengguna dan mapping hak akses | BRD FU-1 |
| Admin | Konfigurasi parameter rekonsiliasi, perpajakan, perhitungan settlement berbasis master kontrak mitra | BRD FU-1, FU-2 |
| Petugas Helpdesk Jasa Keuangan | Monitoring transaksi berjalan; penanganan komplain; pembatalan; push payment | BRD FU-1, FU-3 |
| Petugas Rekonsiliasi | Melihat data tereplikasi; verifikasi & persetujuan tingkat 1 LHR | BRD FU-1, FU-6, FU-8 |
| Asisten Manajer Rekonsiliasi | Verifikasi ulang & validasi akhir LHR (kunci data) | BRD FU-1, FU-8 |
| Petugas Administrasi Operasi Jasa Keuangan | Monitoring & pengecekan draft Nota Settlement; persetujuan tahap 1 (Maker) | BRD FU-1, FU-13 |
| Manajer Administrasi Operasi Jasa Keuangan | Pemeriksaan Nota Settlement tahap 2 (Checker) | BRD FU-1, FU-13 |
| VP Operasi Jasa Keuangan | Persetujuan akhir Nota Settlement (Approver); keputusan lingkup/prioritas | BRD FU-1, FU-13 |
| Petugas Pengelolaan Settlement | Tindak lanjut Nota Settlement final; input sumber rekening pos | BRD FU-1, FU-15 |
| Petugas Operation Control Financial Services (OCFS) | Eksekusi pembatalan data (data cancellation) terkendali + mandatory remarks | BRD FU-1, FU-9 |

## Pengambil Keputusan vs Pemberi Pengaruh

| Peran | Klasifikasi | Catatan | Source |
|---|---|---|---|
| VP Operasi Jasa Keuangan | Pengambil keputusan akhir | Menentukan ruang lingkup/prioritas; approver akhir Nota Settlement | [Q6: A] |
| Manajer terkait (Rekonsiliasi, Administrasi, Helpdesk, Pengendalian Operasi) | Pemberi pengaruh | Memberi masukan atas kebutuhan & prioritas | [Q6: A] |
| Manajer Demand & Program Management | Pemberi pengaruh / koordinator kebutuhan | Mengoordinasikan requirement (asal NDE) | BRD §1.1, [Q4: A] |
| Sub Direktorat Digital Services | Pemberi pengaruh (teknis) | Keputusan teknis & pengembangan | [Q5: E] |

## Kebutuhan Komunikasi

| Kebutuhan | Detail | Source |
|---|---|---|
| Laporan kemajuan berkala | Progress disampaikan berkala ke manajemen Operasi Jaskug & Digital Services | [Q7: A] |
| Persetujuan bertahap per milestone | Mengikuti alur requirement → desain → pengembangan (sejalan dengan tahapan AI-DLC) | [Q7: A] |

## Assumptions & Open Questions

- [assumption] Irama pelaporan spesifik (mingguan/bulanan) dan format laporan kemajuan belum ditetapkan; mengikuti mekanisme standar organisasi sampai ditentukan.
- [assumption] Keterlibatan unit perpajakan dan tim SAP sebagai validator formula fee/pajak/jurnal perlu dikonfirmasi (sejalan dengan AS-6 BRD).
