# Proposal — BengkelCheck (2026-10-22)

## Apa ini
Web app satu halaman: **kalkulator harga wajar servis motor + detektor "ditembak bengkel"**.
Dua mode:
1. **Rakit Estimasi** (sebelum ke bengkel) — pilih jenis motor (matic/bebek/sport),
   checklist servis yang dibutuhkan dari katalog berkategori → keluar rentang total
   wajar (RpX–RpY) yang bisa disalin jadi "bekal" untuk ditunjukkan ke montir.
2. **Cek Tagihan** (sesudah dari bengkel) — masukkan item + harga yang ditagih →
   tiap item dinilai WAJAR / AGAK MAHAL / KEMAHALAN / MURAH BANGET, lalu verdict
   keseluruhan dicap dengan stempel animasi: **WAJAR**, **WASPADA**, atau **DITEMBAK!**

## Kenapa ide ini mengejutkan
Hampir semua pemilik motor di Indonesia pernah merasa "kok mahal banget ya" pas
bayar di bengkel — tapi tidak ada yang tahu angka wajarnya berapa. Ini trauma
kolektif yang belum pernah ada tool-nya. Efek kejutnya: "kok kepikiran sih bikin
ini" + "gila, ini beneran berguna" — orang akan screenshot hasilnya buat nego di
bengkel. Bukan kalkulator generik; ini **senjata negosiasi**.

## Kenapa bentuk web app (bukan package/CLI/game)
- Target user = pemilik motor umum (bukan developer) → harus bisa dibuka dari link,
  jalan di HP, tanpa install.
- Rotasi program: web(16) → game(17) → npm(18) → dart(19) → game(20) → CLI(21) →
  **web(22)**. Sehat.
- Interaksi visual (checklist, stempel verdict) butuh UI — web paling pas.

## Kenapa TANPA backend
Seluruh data (katalog ±25 layanan + rentang harga) statis hasil riset; logika
verdict murni komputasi client-side. Tidak ada login, tidak ada data antar-user.
Backend = over-engineering (dilarang SOP: jangan pakai backend biar kelihatan canggih).

## Data harga
Riset 2026-10-22 dari 5 sumber (gakorpan.com, otoriset.blogspot.com,
supernews.co.id, otohans.com, 530towing.com): rentang biaya servis motor matic
2026 di Indonesia. Angka dipakai sebagai **rentang wajar** (min–max), dengan
disclaimer jujur: estimasi Jabodetabek, bengkel umum; bengkel resmi bisa 20–40%
lebih tinggi; harga beda daerah.

## Risiko & batasan jujur
- Harga adalah ESTIMASI, bukan vonis — verdict = bahan negosiasi, bukan tuduhan.
- Katalog mencakup servis umum; kasus aneh (modif, part racing) di luar scope.
- Satu jenis motor per sesi (beda tipe = beda katalog CVT/rantai).
