# Open Questions & Inkonsistensi BRD — untuk Konfirmasi Tim Bisnis

> Daftar hal yang perlu dikonfirmasi/diputuskan tim bisnis sebelum masuk desain detail. Masing-masing menyebut lokasi di BRD, dampak, dan usulan.

## OQ-1. Simulasi Net Settlement — bergantung pada skema pembayaran komponen biaya (BRD hal. 62, FU-12) — DIPUTUSKAN

- **Temuan:** Tabel simulasi menampilkan Net Settlement Rp9.967.500, padahal 10.000.000 − 27.500 (PPN) + 5.000 (PPh 23) = 9.977.500. Baris "Minus Fee Mitra" kosong, dan ada salah ketik "10.000.0000".
- **Keputusan (user):** Formula net settlement **bergantung pada skema pembayaran tiap komponen biaya** (Biaya Admin / Biaya Jaringan / Sharing Fee) yang dikonfigurasi di parameter FU-2. Artinya net settlement tidak memakai satu formula tetap: tiap komponen biaya bisa punya perlakuan berbeda (mis. langsung dibayarkan/potong BSU vs invoice, exclude/include PPN, pihak settle berbeda) sehingga memengaruhi apakah komponen itu menambah/mengurangi nilai yang disettle ke mitra.
- **Implikasi untuk FR-12:** Engine kalkulasi net settlement harus menghitung per komponen biaya aktif berdasarkan konfigurasi FU-2 (jenis komponen, flat/persen, exclude/include PPN, mekanisme pembayaran, pihak settle), lalu mengakumulasikannya menjadi Net Settlement. Angka contoh pada BRD hal. 62 adalah ilustrasi satu skema, bukan formula universal.
- **Tindak lanjut:** Pada tahap desain, definisikan matriks perlakuan net per komponen biaya x skema pembayaran, dan validasikan dengan tim keuangan/pajak. Contoh angka BRD diperlakukan sebagai salah satu kasus uji, bukan acuan tunggal.

## OQ-2. Referensi FU salah (BRD FU-8 poin 3)

- **Temuan:** FU-8 menyebut "skema pembatalan data FU-8"; seharusnya merujuk **FU-9** (mekanisme pembatalan/inject).
- **Dampak:** Rendah (kejelasan dokumen), tetapi perlu dikoreksi agar jejak requirement rapi.
- **Usulan:** Koreksi referensi menjadi FU-9. Sudah diakomodasi di FR-8 (perbaikan via FR-9).

## OQ-3. Perlakuan transaksi tepat Rp10.000.000 pada ambang pembatalan (FU-3 / FR-3.2)

- **Temuan:** BRD menyebut "di bawah 10.000.000" diproses langsung dan "di atas 10.000.000" lewat approval, tetapi tidak menyebut perlakuan untuk nominal **tepat** Rp10.000.000.
- **Dampak:** Ambiguitas business rule pada pembatalan (SC-3, keberhasilan 100%).
- **Usulan (perlu dikonfirmasi):** nominal tepat Rp10.000.000 mengikuti jalur approval (≥ 10.000.000 butuh persetujuan). Mohon ditegaskan.

## OQ-4. "Tanpa penolakan" vs perbaikan & generate ulang (SC-8 vs FU-8)

- **Temuan:** SC-8 menyatakan verifikasi LHR "tanpa mekanisme penolakan atau pengembalian data", namun FU-8 poin 3 memungkinkan perbaikan data (via pembatalan/inject) dan generate ulang draft LHR.
- **Dampak:** Menentukan apakah petugas/asmen boleh menolak/mengembalikan, atau hanya memperbaiki lewat jalur koreksi terkendali.
- **Usulan (perlu dikonfirmasi):** tidak ada "reject" formal pada LHR; perbaikan hanya melalui mekanisme pembatalan/inject (FR-9) lalu generate ulang, sebelum validasi akhir mengunci data. Mohon ditegaskan.

## OQ-5. SLA Auto-Approve pada Nota Settlement (FU-13 poin 6 / FR-13.6) — DIPUTUSKAN

- **Temuan:** Nota Settlement otomatis disetujui (Auto-Approve) bila otorisator tidak merespons sampai lewat jam SLA.
- **Keputusan (user):** SLA Auto-Approve **diizinkan ada** untuk Nota Settlement, sesuai FU-13 poin 6.
- **Implikasi untuk FR-13.6:** Sistem membaca parameter SLA Auto-Approve (Jam) dari konfigurasi (FU-2); bila otorisator tidak memutuskan sampai lewat jam SLA, sistem mengeksekusi status Auto-Approve agar siklus pelimpahan ke mitra tetap lancar.
- **Tindak lanjut (saran kontrol internal):** pastikan setiap Auto-Approve tercatat jelas pada audit trail (status "Auto-Approved", tahap mana, waktu, parameter SLA yang berlaku) agar tetap dapat ditelusuri. Pertimbangkan notifikasi ke otorisator sebelum SLA tercapai. (Keputusan utama: fitur diizinkan.)

## OQ-6. Mekanisme integrasi internal belum ditetapkan (SOPP, SAP, Remittance, CGS, CRM)

- **Temuan:** BRD menyatakan beberapa mekanisme integrasi "ditentukan berdasarkan hasil analisis tim teknis"; API belum tersedia [Q10].
- **Dampak:** Jalur FR-3.3, FR-4, FR-7, FR-9, FR-15 bergantung pada integrasi ini.
- **Keputusan yang sudah diambil [Q10]:** integrasi di-mock dulu di belakang antarmuka port/adapter, siap ditukar API nyata.
- **Klarifikasi "kontrak data":** kontrak data = kesepakatan bentuk data yang dipertukarkan tiap sistem (field + tipe + wajib/opsional, format, protokol/cara panggil, arah data, serta respons & error). Contoh: untuk SAP mencakup nomor dokumen, kode GL/COA, nominal, mata uang, tanggal posting; untuk SOPP mencakup struktur SOPP-3A dan status payment.
- **Tidak memblokir user stories:** kontrak data BUKAN prasyarat user stories. User stories berada di level kebutuhan pengguna, bukan detail teknis field/protokol. Kontrak data baru diperlukan pada tahap desain & implementasi adapter. User story integrasi ditulis "sadar-mock" (mis. menarik SOPP-3A melalui adapter; bila API belum ada, pakai data mock yang menyerupai struktur SOPP-3A).
- **Tindak lanjut (saat desain):** definisikan kontrak data tiap sistem — SOPP-3A dan contoh jurnal SAP sudah ada di BRD sebagai titik awal spesifikasi field; perkirakan waktu ketersediaan API tiap sistem.

## OQ-7. Perluasan lingkup melebihi fokus utama BRD (dikonfirmasi pengguna)

- **Temuan:** BRD Batasan 6.2 no. 1 membatasi ke skema "Pos hanya generate data"; keputusan pengguna [Q8] menghapus batasan ini dan memasukkan seluruh skema rekonsiliasi (termasuk FU-5 & FU-11).
- **Dampak:** Lingkup lebih luas; perlu kesepakatan sumber daya & timeline (BRD mengkategorikan proyek "Menengah" 3–6 bulan untuk lingkup yang lebih sempit).
- **Status:** Keputusan eksplisit pengguna; dicatat sebagai perubahan lingkup, bukan konflik. Mohon konfirmasi timeline/sumber daya menyesuaikan lingkup baru.

## OQ-8. Label kerahasiaan dokumen & repo

- **Temuan:** BRD berlabel RAHASIA dan telah diunggah ke repository GitHub.
- **Dampak:** Risiko kebocoran bila repo publik.
- **Usulan:** pastikan repo privat, atau keluarkan dokumen RAHASIA dari git. (Di luar requirement produk, namun relevan untuk tata kelola.)
