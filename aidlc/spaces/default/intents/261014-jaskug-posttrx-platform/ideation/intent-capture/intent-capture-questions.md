# Intent Capture — Pertanyaan Klarifikasi

## Sources

- [desc] Initial description: "Build the post-transaction Jaskug platform from the BRD at base/BRD_Tools_Monitoring,_Rekonsiliasi_dan_Settlement_Transaksi_Jasa.pdf: four tools - real-time transaction monitoring, reconciliation, settlement-note calculation and generation, and settlement with settlement monitoring - plus RBAC and partner-contract parameter configuration as shared foundations. Start with requirements."
- [scope] Workflow-selected scope: `enterprise`.

> Catatan: Dokumen BRD (document_id 01a138ba-ca54-7bf8-ac7d-c0ece56f21ec) sudah ditambahkan ke knowledge base sebagai referensi. Isinya saya pakai untuk menyusun pertanyaan di bawah; jawaban Anda yang akan menjadi dasar artefak, bukan dokumen itu sendiri.

---

Isi setiap `[Answer]:` dengan memilih huruf opsi (boleh lebih dari satu bila relevan) atau pilih `X. Other` lalu tulis jawaban Anda sendiri. Pilihan default/aman sudah saya tandai dengan "(rekomendasi)".

## Q1. Apa masalah bisnis utama yang ingin diselesaikan platform ini?

- A. Proses rekonsiliasi dan settlement transaksi jasa keuangan masih manual, lambat, dan rawan kesalahan (rekomendasi)
- B. Belum ada pemantauan transaksi real-time dan penanganan komplain/pembatalan yang terstruktur
- C. Risiko fraud dan ketiadaan jejak audit yang konsisten pada koreksi data
- D. Perhitungan fee/pajak/settlement dan penjurnalan ke SAP belum terotomasi
- E. Semua masalah A–D sekaligus (digitalisasi menyeluruh ekosistem post-transaction)
- X. Other (please specify)

[Answer]: E. Semua masalah A–D sekaligus (digitalisasi menyeluruh ekosistem post-transaction)

## Q2. Siapa pelanggan/pengguna utama platform ini dan pain point mereka?

- A. Pengguna internal PT Pos (Helpdesk, Petugas & Asisten Manajer Rekonsiliasi, Petugas/Manajer Administrasi, VP, Petugas Settlement, OCFS, Admin) (rekomendasi)
- B. Mitra eksternal jasa keuangan (akses laporan dan rekonsiliasi mandiri)
- C. Keduanya: pengguna internal sebagai pengguna utama, mitra sebagai pengguna sekunder (rekomendasi)
- D. Not yet defined
- X. Other (please specify)

[Answer]: C. Keduanya: pengguna internal sebagai pengguna utama, mitra sebagai pengguna sekunder

## Q3. Seperti apa definisi "sukses" dan metrik apa yang paling penting?

- A. Akurasi: 100% klasifikasi rekonsiliasi (matched/forced/suspect), 99% akurasi pencarian/tampilan, 100% akurasi kalkulasi fee/pajak/settlement (sesuai SC-5, SC-6, SC-12)
- B. Otomasi: 100% keberhasilan generate EOD dan posting jurnal SAP tanpa intervensi manual (SC-4, SC-15)
- C. Keandalan & kontrol: 100% jejak audit pada approval berjenjang, uptime portal mitra 99,9% (SC-11, SC-13)
- D. Efisiensi operasional: pengurangan waktu & intervensi manual pada proses rekonsiliasi–settlement
- E. Semua metrik A–D (rekomendasi)
- X. Other (please specify)

[Answer]: E. Semua metrik A–D

## Q4. Apa pemicu utama inisiatif ini sekarang (why now)?

- A. Arahan/kebutuhan resmi internal (NDE simplifikasi tools rekonsiliasi & MoM observasi) (rekomendasi)
- B. Kebutuhan kepatuhan, audit, dan mitigasi fraud
- C. Tekanan operasional akibat volume transaksi dan proses manual
- D. Semua A–C
- X. Other (please specify)

[Answer]: D. Semua A–C

## Q5. Siapa pemangku kepentingan (stakeholder) kunci dan apa yang menjadi perhatian masing-masing?

- A. Unit Operasi Jasa Keuangan (pemilik proses bisnis & akurasi rekonsiliasi/settlement) (rekomendasi)
- B. Sub Direktorat Digital Services (pemilik sistem & pengembangan) (rekomendasi)
- C. Tim Keuangan/SAP (integritas penjurnalan) dan unit terkait perpajakan
- D. Mitra eksternal (transparansi data & laporan)
- E. Semua A–D (rekomendasi)
- X. Other (please specify)

[Answer]: E. Semua A–D

## Q6. Siapa yang berwenang memutuskan ruang lingkup/prioritas, dan siapa yang memengaruhi keputusan tersebut?

- A. VP Operasi Jasa Keuangan sebagai pengambil keputusan akhir; Manajer terkait sebagai pemberi pengaruh (rekomendasi)
- B. Manajer Demand & Program Management sebagai koordinator kebutuhan
- C. Komite/forum bersama Operasi Jaskug + Digital Services
- D. Not yet defined
- X. Other (please specify)

[Answer]: A. VP Operasi Jasa Keuangan sebagai pengambil keputusan akhir; Manajer terkait sebagai pemberi pengaruh

## Q7. Apakah ada kebutuhan komunikasi atau irama pelaporan (reporting cadence) selama pengembangan?

- A. Laporan kemajuan berkala ke manajemen Operasi Jaskug & Digital Services (rekomendasi)
- B. Persetujuan bertahap mengikuti milestone (requirement → desain → pengembangan)
- C. Belum ada kebutuhan khusus / mengikuti mekanisme standar organisasi
- D. Not yet defined
- X. Other (please specify)

[Answer]: A. Laporan kemajuan berkala ke manajemen Operasi Jaskug & Digital Services

## Q8. Apa yang DILUAR cakupan versi pertama ini? (ruang lingkup sudah dipilih: `enterprise` sebagai tingkat proses; pertanyaan ini soal batas produk)

Catatan: BRD membatasi fokus utama pada skema "Pos hanya generate data dan mengirim ke mitra". Pilih apa yang sebaiknya DIKECUALIKAN dari versi pertama.

- A. Rekonsiliasi penuh berbasis data mitra (FU-5) dan portal rekonsiliasi mandiri mitra (FU-11) dikecualikan dulu; fokus pada skema "Pos generate saja" (rekomendasi, sesuai Batasan BRD 6.2)
- B. Semua empat tools + fondasi masuk versi pertama (termasuk FU-5 & FU-11)
- C. Eksekusi pembayaran dana ke mitra tetap diluar lingkup (sistem hanya hasilkan Nota Settlement & data pembayaran) (rekomendasi, sesuai Batasan BRD)
- D. Integrasi nyata ke SOPP/SAP/Sistem Remitansi di-stub/mock dulu pada versi pertama bila API belum tersedia
- E. Belum ditentukan / perlu diskusi
- X. Other (please specify)

[Answer]: X. Other — Jangan dibatasi ke skema "Pos generate saja". Seluruh skema rekonsiliasi ikut (termasuk FU-5 rekonsiliasi berbasis data mitra dan FU-11 portal rekonsiliasi mandiri mitra). Keempat tools (monitoring, rekonsiliasi, kalkulasi & nota settlement, settlement & monitoring settlement) digabung menjadi SATU platform end-to-end. Eksekusi/transfer pembayaran dana ke mitra tetap DI LUAR lingkup (sistem hanya menghasilkan Nota Settlement dan data pembayaran), sesuai Batasan BRD.

## Q9. Teknologi/stack: apakah sudah ada ketentuan, atau bebas ditentukan pada tahap desain nanti?

- A. Bebas ditentukan nanti pada tahap desain (rekomendasi untuk tahap requirement ini)
- B. Sudah ada ketentuan stack tertentu (mohon sebutkan di Other)
- C. Harus mengikuti standar/teknologi yang sudah dipakai Digital Services PT Pos
- D. Not yet defined
- X. Other (please specify)

[Answer]: A. Bebas ditentukan nanti pada tahap desain (tech stack belum ditentukan).

## Q10. Ketersediaan integrasi eksternal (SOPP, SAP, Sistem Remitansi/CGS/CRM): apakah API sudah tersedia?

- A. Belum tersedia / belum pasti — perlu di-mock dulu agar pengembangan tidak terblokir (rekomendasi bila belum pasti)
- B. Sebagian sudah tersedia (mohon sebutkan yang mana di Other)
- C. Sudah tersedia semua dan siap diintegrasikan
- D. Not yet defined
- X. Other (please specify)

[Answer]: X. Other — Integrasi hanya ke sistem internal PT Pos: SOPP, SAP, Remittance, CGS, dan CRM. Saat ini API belum tersedia, tetapi akan dipersiapkan kemudian; untuk versi pengembangan, integrasi ini di-mock/di-stub dulu agar pengembangan tidak terblokir.

## Assumptions & Open Questions

- [assumption] Beberapa inkonsistensi pada BRD perlu klarifikasi tim bisnis sebelum masuk desain: simulasi Net Settlement di contoh (hal. 62) tidak konsisten (10.000.000 − 27.500 + 5.000 = 9.977.500, bukan 9.967.500); referensi "skema pembatalan data FU-8" seharusnya FU-9; perlakuan transaksi tepat Rp10.000.000 pada ambang pembatalan belum tegas; SC-8 menyatakan "tanpa penolakan" namun FU-8 memungkinkan perbaikan & generate ulang; SLA Auto-Approve pada Nota Settlement (FU-13) berisiko dari sisi kontrol keuangan.
