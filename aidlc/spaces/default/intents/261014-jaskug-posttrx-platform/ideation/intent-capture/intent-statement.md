# Intent Statement — Platform Post-Transaction Jasa Keuangan (Jaskug)

> Dokumen ini disusun pada tahap Intent Capture & Framing berdasarkan BRD "Tools Monitoring, Rekonsiliasi dan Settlement Transaksi Jasa Keuangan" (PT Pos Indonesia, v1.0.0) dan jawaban klarifikasi Q1–Q10.

## Problem Statement

Proses pasca-transaksi (post-transaction) pada layanan Jasa Keuangan PT Pos Indonesia saat ini masih manual, lambat, dan rawan kesalahan. Masalah yang ingin diselesaikan mencakup empat hal sekaligus [Q1: E]:

- Rekonsiliasi dan settlement transaksi masih manual sehingga lambat dan rawan error.
- Belum ada pemantauan transaksi real-time serta penanganan komplain/pembatalan yang terstruktur.
- Terdapat risiko fraud dan ketiadaan jejak audit yang konsisten pada koreksi data.
- Perhitungan fee/pajak/settlement dan penjurnalan ke SAP belum terotomasi.

Prinsip inti platform: **data internal PT Pos Indonesia diakui sebagai Single Source of Truth** untuk seluruh proses replikasi, rekonsiliasi, dan settlement.

## Target Customer

Pengguna utama adalah pengguna **internal** PT Pos Indonesia; mitra eksternal menjadi pengguna sekunder [Q2: C].

- **Internal (utama):** Super Admin, Admin, Petugas Helpdesk Jasa Keuangan, Petugas Rekonsiliasi, Asisten Manajer Rekonsiliasi, Petugas Administrasi Operasi Jasa Keuangan, Manajer Administrasi Operasi Jasa Keuangan, VP Operasi Jasa Keuangan, Petugas Pengelolaan Settlement, dan Petugas Operation Control Financial Services (OCFS).
- **Eksternal (sekunder):** Mitra jasa keuangan — menerima/mengakses laporan hasil rekonsiliasi dan melakukan rekonsiliasi mandiri melalui portal.

Pain point: beban kerja manual yang tinggi, waktu penyelesaian yang lama, risiko selisih finansial, serta kebutuhan transparansi data bagi mitra.

## Success Metrics

Keberhasilan diukur dari gabungan metrik berikut [Q3: E], sesuai Kriteria Keberhasilan BRD:

- **Akurasi:** 100% akurasi klasifikasi rekonsiliasi (matched/forced/suspect) [SC-5], ≥99% akurasi tampilan/pencarian data transaksi [SC-6], 100% akurasi kalkulasi fee/pajak/net settlement [SC-12].
- **Otomasi:** 100% keberhasilan proses generate End of Day [SC-4] dan 100% keberhasilan posting jurnal ke SAP tanpa intervensi manual [SC-15].
- **Keandalan & kontrol:** 100% jejak audit pada alur persetujuan berjenjang [SC-13], uptime portal mitra 99,9% [SC-11].
- **Efisiensi operasional:** pengurangan waktu dan intervensi manual pada seluruh rantai rekonsiliasi–settlement.

## Initiative Trigger

Pemicu inisiatif adalah **arahan/kebutuhan resmi internal** [Q4: A]: NDE VP Operasi Giro perihal Requirement Simplifikasi Tools Rekonsiliasi dan NDE Manajer Demand & Program Management perihal MoM Observasi Rekonsiliasi Operasi Jasa Keuangan. Terdapat urgensi untuk melakukan simplifikasi, digitalisasi, dan peningkatan keamanan menyeluruh pada ekosistem tools rekonsiliasi, termasuk kebutuhan kepatuhan, audit, dan mitigasi fraud.

## Initial Scope Signal

- **Scope proses (workflow-selected):** `enterprise` — platform finansial berskala besar dengan kontrol dan audit ketat.
- **Batas produk (dikonfirmasi pengguna) [Q8]:**
  - Platform dibangun sebagai **satu produk end-to-end** yang menyatukan empat tools: (1) Monitoring Transaksi Real-time, (2) Rekonsiliasi, (3) Kalkulasi & Generate Nota Settlement, (4) Settlement & Monitoring Settlement.
  - **Tidak ada** batasan "Pos hanya generate data saja". Batasan tersebut pada BRD (Batasan 6.2 no. 1) dinyatakan **salah dan dihapus**; **seluruh skema rekonsiliasi ikut**, termasuk rekonsiliasi berbasis data mitra (FU-5) dan portal rekonsiliasi mandiri mitra (FU-11).
  - Fondasi bersama: autentikasi + RBAC (FU-1) dan konfigurasi parameter kontrak mitra (FU-2).
  - **Di luar lingkup:** eksekusi/transfer pembayaran dana ke mitra. Sistem hanya menghasilkan Nota Settlement dan menyediakan data yang dibutuhkan untuk pembayaran dan penjurnalan.
- **Teknologi [Q9: A]:** stack belum ditentukan; keputusan teknis ditetapkan pada tahap desain.
- **Integrasi [Q10]:** hanya ke sistem **internal** PT Pos Indonesia — SOPP, SAP, Remittance, CGS, dan CRM. API belum tersedia saat ini namun akan dipersiapkan; untuk pengembangan, integrasi ini **di-mock/di-stub dulu** agar tidak memblokir.

## Assumptions & Open Questions

- [assumption] Beberapa inkonsistensi BRD perlu konfirmasi tim bisnis sebelum desain detail (lihat daftar lengkap di `open-questions.md`): simulasi Net Settlement contoh (hal. 62) tidak konsisten; referensi "skema pembatalan data FU-8" seharusnya FU-9; perlakuan transaksi tepat Rp10.000.000 pada ambang pembatalan belum tegas; SC-8 menyatakan "tanpa penolakan" namun FU-8 memungkinkan perbaikan & generate ulang; SLA Auto-Approve pada Nota Settlement (FU-13) berisiko dari sisi kontrol keuangan.
- [assumption] Keputusan memasukkan seluruh skema rekonsiliasi memperluas lingkup melebihi fokus utama BRD; ini keputusan eksplisit pengguna, bukan konflik.
