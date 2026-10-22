# Design — BengkelCheck

## Pendekatan teknis
- **Satu file HTML statis** (HTML + CSS + JS inline, tanpa framework/build step),
  mengikuti pola `daily-berapa-sih` yang terbukti gampang di-export ke repo.
  Static artifact → bisa di-share publik + di-export.
- **Tanpa backend** (lihat proposal). Tanpa fetch runtime (REQ-09).
- State buckets (ARCHITECTURE.md, lima keranjang):
  - **Client UI state**: mode aktif (estimasi/tagihan), jenis motor, item tercentang,
    baris tagihan, hasil verdict — semua di memori + `localStorage` hanya untuk
    jenis motor terakhir.
  - **Static data**: `BENGKEL_DB` (konstanta, lihat bawah).
  - **Derived**: total estimasi & verdict = fungsi murni dari state (REQ-11).
  - Server state: tidak ada. Form state: input harga per baris (sementara).

## Struktur halaman (mobile-first, satu kolom)
1. **Hero**: nama "BengkelCheck", tagline "Cek harga wajar servis motor.
   Biar nggak ditembak.", ilustrasi SVG kunci inggris. Pilih jenis motor
   (3 kartu: Matic / Bebek / Sport).
2. **Tab mode**: [Rakit Estimasi] [Cek Tagihan] — toggle besar.
3. **Mode Estimasi**: katalog per kategori (accordion atau grup berjudul),
   tiap item = checkbox card (nama + rentang + catatan). Panel struk lengket
   di bawah: total rentang + tombol "Salin bekal".
4. **Mode Cek Tagihan**: form tambah baris (select layanan + input Rp +
   tombol tambah), daftar baris (nama, harga input, badge verdict per item,
   tombol hapus). Panel struk: total ditagih vs total wajar + **stempel verdict**.
5. **Bekal Nego**: 5 tips kartu singkat.
6. **Footer**: disclaimer (REQ-07) + kredit "Program 1 Hari 1 Project".

## Visual direction (impeccable, mode Operate)
- Dunia visual: **"struk bengkel"** — kartu hasil berbentuk struk kertas
  (putih, tepi zigzag/putus-putus) di atas background charcoal gelap (#16130f).
- Aksen: safety-orange (#ff6b1a) ala lampu bengkel; stempel merah/hijau/amber.
- Tipografi: Plus Jakarta Sans (body — font karya desainer Indonesia, cocok),
  "Archivo Black"/condensed untuk display, JetBrains Mono untuk angka Rupiah.
- Ikon: SVG inline (kunci, rem, ban, aki, oli). NOL emoji di label.
- Signature elements: (1) stempel karet verdict yang "dibanting",
  (2) struk kertas dengan total bergaris putus-putus,
  (3) count-up angka Rupiah.

## Animasi (delight wajib)
- Stempel verdict: keyframes `stamp-in` — scale 2.2→1 + rotate(-8deg),
  0.35s cubic-bezier(.2,2.4,.4,1), lalu menetap. `prefers-reduced-motion` → statis.
- Count-up total: requestAnimationFrame ±600ms.
- Tab mode: geser indikator + fade konten 150ms.
- Tanpa library animasi (cukup CSS + rAF) — file tetap ringan.

## Data: BENGKEL_DB (min/max Rupiah, estimasi Jabodetabek 2026, bengkel umum)
Kategori Rutin: oli-mesin 45000–90000 | oli-gardan 20000–40000 (matic) |
servis-ringan 60000–120000 | tune-up 120000–250000 | infus-injeksi 80000–150000.
Kategori CVT (matic): servis-cvt 80000–150000 | v-belt 150000–350000 |
roller 80000–200000 | kampas-ganda 150000–300000.
Kategori Rantai (bebek/sport): rantai-gear 150000–300000.
Kategori Pengereman: kampas-depan 50000–120000 | kampas-belakang 40000–100000 |
minyak-rem 30000–60000.
Kategori Kelistrikan: aki 180000–350000 | busi 20000–45000 | lampu-depan 20000–80000.
Kategori Ban & Kaki: ban-depan 200000–450000 | ban-belakang 250000–500000 |
tambal-tubeless 20000–40000 | shock-belakang 200000–450000 | komstir 100000–250000.
Kategori Mesin: overhaul 600000–1500000.
Kategori Lainnya: filter-udara 30000–70000 | kabel-gas 30000–80000 |
cuci-steam 15000–30000.
Total 25 item. Tiap item: id, nama, kategori, min, max, untuk[], catatan.

## Verdict (fungsi murni, REQ-11)
`nilaiKewajaran(harga, min, max)` → 'murah-mencurigakan' | 'wajar' |
'agak-mahal' | 'kemahalan' sesuai ambang REQ-04.
`verdictKeseluruhan(nilai[])` → 'WAJAR' | 'WASPADA' | 'DITEMBAK!' sesuai REQ-04.
Format Rupiah: `Intl.NumberFormat('id-ID')`.

## Anti-AI-slop & IP
- Copy: Bahasa Indonesia santai, konkret ("biar nggak ditembak"), tanpa
  kata sifat generik bertumpuk. Tanpa emoji di label.
- IP: semua ikon digambar sendiri sebagai SVG inline; tidak ada aset pihak ketiga.
