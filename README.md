# BengkelCheck

**Kalkulator harga wajar servis motor + detektor "ditembak bengkel".**
Pilih jenis motormu (matic/bebek/sport), lalu dua mode: **Rakit Estimasi** —
centang servis yang dibutuhkan dan dapatkan rentang harga wajar sebagai "bekal"
sebelum ke bengkel; **Cek Tagihan** — masukkan harga yang ditagih bengkel dan
dapatkan verdict per item (WAJAR / AGAK MAHAL / KEMAHALAN / MURAH BANGET) plus
stempel verdict keseluruhan: **WAJAR**, **WASPADA**, atau **DITEMBAK!**

> "Oli + kampas rem depan harusnya Rp95–210rb. Ditagih 350rb? DITEMBAK!"

## Kenapa ide ini dipilih

Hampir semua pemilik motor di Indonesia pernah merasa "kok mahal banget ya" saat
bayar di bengkel — tapi tidak ada yang tahu angka wajarnya berapa. Ini trauma
kolektif yang belum pernah ada tool-nya. Efek kejutnya dua lapis: "kok kepikiran
sih" + "gila, ini beneran berguna" — hasilnya bisa di-screenshot buat nego di
bengkel. Ini senjata negosiasi, bukan kalkulator generik.

Program "1 Hari 1 Project", edisi ketujuh — 2026-10-22. Rotasi: web → game →
npm → dart → game → CLI → **web**.

## Fitur

- **Pilih jenis motor** — Matic / Bebek / Sport; katalog otomatis difilter
  (item CVT hanya untuk matic, rantai hanya bebek/sport); pilihan diingat
  di `localStorage`
- **Rakit Estimasi** — checklist 25 layanan per kategori → total rentang wajar
  dengan animasi count-up + tombol "Salin bekal" (ringkasan teks ke clipboard)
- **Cek Tagihan** — tambah baris (layanan + harga ditagih) → badge verdict per
  item + tombol hapus; struk menampilkan total ditagih vs rentang wajar
- **Stempel verdict animasi** — WAJAR (hijau) / WASPADA (amber) / DITEMBAK!
  (merah), efek "dibanting"; hormat `prefers-reduced-motion`
- **Mesin verdict murni & teruji** — `nilaiKewajaran()` + `verdictKeseluruhan()`
  tanpa akses DOM; ambang: ≤ max = wajar, ≤ 1.3× max = agak mahal,
  > 1.3× max = kemahalan, < 0.6× min = murah banget
- **Bekal Nego** — 5 tips negosiasi di bengkel
- **Disclaimer jujur** — estimasi Jabodetabek 2026 untuk bengkel umum; bengkel
  resmi/part ori bisa 20–40% lebih tinggi; hasil = bahan negosiasi, bukan vonis
- **100% client-side** — satu file HTML, tanpa fetch data saat dibuka
  (satu-satunya eksternal: Google Fonts via `<link>`)
- Mobile-friendly (nyaman di 360px), Bahasa Indonesia natural, tanpa emoji di label

## Cara pakai

Buka link hasil → pilih jenis motor → pilih mode:

- **Sebelum ke bengkel**: tab *Rakit Estimasi*, centang servis yang dibutuhkan,
  salin bekal, tunjukkan ke montir.
- **Sesudah dari bengkel**: tab *Cek Tagihan*, masukkan tiap item + harganya,
  lihat stempel verdict. Kalau DITEMBAK!, tanya rinciannya.

Tidak ada akun, tidak ada yang tersimpan di server.

## Cara menjalankan / mengembangkan dari source

File utama: `src/index.html` — satu file statis, langsung bisa dibuka di browser:

```bash
# cara 1: buka langsung
xdg-open src/index.html        # Linux
open src/index.html            # macOS

# cara 2: serve lokal (opsional)
npx serve src
```

### Uji logika verdict (Node, tanpa browser)

Fungsi verdict + data katalog sengaja murni (tanpa DOM) sehingga bisa diuji
langsung dari file HTML:

```bash
node -e "
const fs = require('fs');
const html = fs.readFileSync('src/index.html', 'utf8');
const script = html.match(/<script>([\s\S]*)<\/script>/)[1];
eval(script.slice(script.indexOf('var SEMUA'), script.indexOf('/* ================= STATE')));
console.log(nilaiKewajaran(400000, 150000, 350000)); // 'agak-mahal'
console.log(verdictKeseluruhan(['wajar','kemahalan'])); // 'DITEMBAK!'
"
```

## Tech stack

- Satu file HTML statis (HTML + CSS + JS inline, tanpa framework, tanpa build step)
- Google Fonts: Plus Jakarta Sans (body), Archivo Black (display), JetBrains Mono (angka)
- Animasi: CSS keyframes (stempel) + `requestAnimationFrame` (count-up)
- Penyimpanan: `localStorage` hanya untuk jenis motor terakhir

## Struktur folder

```
2026-10-22-bengkel-check/
├── README.md                 # dokumentasi ini
├── src/
│   └── index.html            # aplikasi lengkap (satu file)
└── openspec/
    └── changes/
        └── archive/
            └── 2026-10-22-bengkel-check/
                ├── proposal.md
                ├── specs/bengkel-check/spec.md
                ├── design.md
                └── tasks.md
```

## Link

- **Live: https://daily-bengkel-check.vercel.app** (deploy Vercel 2026-10-22 atas ACC Junan; terverifikasi HTTP 200 + konten)
- Hasil: web artifact `bengkelcheck` (dibangun 2026-10-22; dibuka lewat app Muse)
- Repo: https://github.com/junanda24/daily-bengkel-check
- Tanggal rilis: 2026-10-22

## Catatan saat build

- **Data harga** (25 layanan, rentang min–max) diriset 2026-10-22 dari 5 sumber:
  gakorpan.com, otoriset.blogspot.com, supernews.co.id, otohans.com,
  530towing.com — kisaran biaya servis motor matic 2026. Dipakai sebagai
  rentang wajar, bukan harga pasti.
- **Ambang verdict** dipilih konservatif: 1.3× max untuk "kemahalan" agar
  bengkel resmi/part ori yang memang lebih mahal tidak langsung divonis;
  label "AGAK MAHAL" pun bernada "masih masuk akal kalau bengkel resmi".
- **Sengaja tidak dibuat**: mode multi-motor sekaligus (beda tipe = beda
  katalog, bikin bingung); database harga per daerah (butuh backend/crowdsourcing);
  riwayat cek tersimpan (privasi + kompleksitas).
- **Verifikasi**: 22/22 uji logika Node hijau (DB 25 item valid, ambang batas
  verdict, agregasi, format Rupiah); scan bersih: tanpa lorem/TODO, tanpa emoji
  di label, tanpa fetch runtime, `prefers-reduced-motion` dihormati.
- Keputusan deploy: awalnya tidak di-deploy (aturan program: Vercel hanya atas
  ACC Junan). Junan memberi ACC pada 2026-10-22 → live di
  https://daily-bengkel-check.vercel.app (project Vercel `daily-bengkel-check`,
  SSO protection dimatikan agar publik bisa membuka).
