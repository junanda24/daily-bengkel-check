# Spec — BengkelCheck

## Requirements

### REQ-01 — Pilih jenis motor
Sistem SHALL menyediakan pilihan jenis motor: Matic, Bebek, Sport.
WHEN user memilih jenis THEN katalog difilter (item CVT hanya untuk matic;
item rantai hanya untuk bebek/sport) dan pilihan disimpan di localStorage.

### REQ-02 — Katalog servis
Sistem SHALL menampilkan katalog ±25 layanan servis, dikelompokkan per kategori
(Rutin, CVT, Rantai, Pengereman, Kelistrikan, Ban & Kaki-kaki, Mesin, Lainnya).
WHEN katalog dirender THEN tiap item menampilkan nama, rentang harga wajar
(Rp min – Rp max), dan satu baris catatan singkat. Tidak ada item tanpa harga.

### REQ-03 — Mode Rakit Estimasi
Sistem SHALL menyediakan mode estimasi: user mencentang layanan yang dibutuhkan.
WHEN ada ≥1 item dicentang THEN sistem menampilkan total rentang wajar
(jumlah min – jumlah max) dalam format Rupiah dengan animasi count-up,
plus tombol "Salin bekal" yang menyalin ringkasan teks ke clipboard.
WHEN tidak ada item dicentang THEN area hasil menampilkan empty state yang jelas
(bukan angka Rp0 yang menyesatkan).

### REQ-04 — Mode Cek Tagihan
Sistem SHALL menyediakan mode cek tagihan: user menambah baris berisi
(layanan dari katalog + harga yang ditagih dalam Rupiah).
WHEN baris ditambahkan THEN sistem menilai tiap item dengan fungsi verdict murni:
- harga < 0.6 × min → MURAH BANGET ("Pastikan part-nya asli.")
- harga ≤ max → WAJAR
- harga ≤ 1.3 × max → AGAK MAHAL ("Masih masuk akal kalau bengkel resmi / part ori.")
- harga > 1.3 × max → KEMAHALAN
WHEN ≥1 baris ada THEN sistem menampilkan verdict keseluruhan:
ada KEMAHALAN → "DITEMBAK!"; ada AGAK MAHAL (tanpa KEMAHALAN) → "WASPADA";
selainnya → "WAJAR". User bisa hapus baris.

### REQ-05 — Stempel verdict
Sistem SHALL menampilkan verdict keseluruhan sebagai stempel karet animasi
(slam: scale dari besar + sedikit rotasi, lalu menetap miring).
Warna: WAJAR = hijau, WASPADA = amber, DITEMBAK! = merah.
Stempel WAJIB menghormati `prefers-reduced-motion` (tampil statis bila diminta).

### REQ-06 — Salin ringkasan
Sistem SHALL menyediakan tombol salin di kedua mode yang menyalin ringkasan
teks Bahasa Indonesia (daftar item + angka + verdict) ke clipboard,
dengan konfirmasi visual "Tersalin!".

### REQ-07 — Disclaimer jujur
Sistem SHALL menampilkan disclaimer: harga adalah estimasi Jabodetabek 2026
untuk bengkel umum; bengkel resmi/part ori bisa lebih tinggi; hasil adalah bahan
negosiasi, bukan vonis. Tidak boleh ada klaim "harga pasti".

### REQ-08 — Kualitas UI
Sistem SHALL: mobile-friendly di 360px; copy Bahasa Indonesia natural;
TANPA emoji di label UI (ikon SVG); TANPA lorem ipsum; TANPA tombol mati;
semua angka Rupiah diformat id-ID.

### REQ-09 — 100% client-side
Sistem SHALL berjalan tanpa fetch data saat runtime (satu file HTML, CSS+JS inline).
Satu-satunya fetch eksternal yang diizinkan adalah font via <link> CDN.

### REQ-10 — Tips negosiasi
Sistem SHALL menampilkan seksi "Bekal Nego" berisi 4–5 tips singkat
(minta rincian part vs jasa, tanya part ori/KW, bandingkan 2 bengkel, dll).

### REQ-11 — Logika murni & teruji
Fungsi verdict + agregasi SHALL berupa fungsi murni tanpa akses DOM
(data di `const BENGKEL_DB`, fungsi `nilaiKewajaran(harga, min, max)` dan
`verdictKeseluruhan(daftarNilai)`), sehingga bisa diuji di Node.
