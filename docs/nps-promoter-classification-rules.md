# NPS Promotor — Rules Klasifikasi Komentar

Dokumen kanonik untuk kartu **"Promoter per Alasan"** di dashboard NPS.
Dibuat: 25 Okt 2026 · Request: NOOR · Data: Supabase `nps_data` (category = `Promoters`)

## Kenapa dua dimensi

Detractor dianalisis per **produk/touchpoint** (kenapa mereka kecewa). Promotor butuh dua hal:

1. **Alasan** — kenapa user puas & mau merekomendasikan Parkee (jawaban utama)
2. **Touchpoint** — produk/layanan apa yang disebut di komentar

Nama touchpoint sengaja **sama dengan kategori detractor** supaya bisa dibandingkan side-by-side
(contoh: Membership 82 promotor vs 32 detractor).

## Dimensi 1 — Alasan (9 kode, urutan prioritas)

| kode | label | pemicu utama |
|---|---|---|
| R1 | Respon Cepat & Tanggap | cepat, cepet, fast respon, gercep, sigap, langsung, beberapa detik, instan, quick, membalas/dibalas |
| R2 | Solutif & Tuntas | solutif, solusi, selesai, solved, beres, tuntas, terbantu, teratasi, ditangani, terjawab |
| R3 | Ramah & Empati | ramah, sabar, sopan, baik, tulus, profesional, perhatian, friendly, empati |
| R4 | Proaktif & Follow-up | proaktif, follow up, inisiatif, konsisten, dipertahankan, dihubungi, amanah, bertanggung jawab |
| R5 | Jelas & Mudah Dipahami | jelas, informasi/infonya, penjelasan, paham, mengerti, detail, informatif, edukatif |
| R6 | Transparan & Amanah | transparan, jujur, aman, tidak dipersulit, terpercaya, uang kembali 100% |
| R7 | Mudah & Praktis (Produk) | praktis, simpel, mudah, efisien, cashless, tanpa antre, modern, fleksibel |
| R8 | Hemat & Promo | murah, promo, diskon, cashback, hemat, gratis, terjangkau |
| R9 | Tanpa Alasan Spesifik | pujian generik / hanya "rekomendasi" / emoji |

**Aturan:** alasan dipilih dari urutan R1 → R8 (first match). Kata pengisi
(`rekomendasi`, `layak`, `wajib`, `sangat`, `sekali`, `mantap`, `bagus`, `parkee`, `terima kasih`, …)
**dibuang dulu** sebelum pencocokan, supaya "bagus banget, rekomendasi" tidak dianggap punya alasan.

## Dimensi 2 — Touchpoint (10 kode, urutan prioritas)

| kode | label | pemicu |
|---|---|---|
| WUZ | Produk Wuzz | wuzz, wizz, voucher, stiker, pick up wuzz, beli produk |
| EV | Produk Bluecharge (EV) | bluecharge, charging, charger, EV, kWh, voltron, spklu, baterai, isi daya |
| MEM | Membership & Approval | membership, member, approval, perpanjang, langganan, kartu member, bulanan |
| REF | Refund & Pembayaran | refund, pengembalian, uang kembali, pembayaran, terpotong, saldo, QRIS |
| TAR | Tarif & Promo | promo, diskon, cashback, murah, tarif, harga, biaya, gratis |
| OPS | Petugas & Lokasi | internet, koneksi, petugas, staff, pengelola, mall, lokasi, gate/palang, mesin, struk |
| APP | Aplikasi & Sistem | aplikasi, app, sistem, online, fitur, top up, scan, QR/barcode, bot, UI |
| CS | Layanan CS & Resolusi | CS, admin, respon, layanan, solutif, ramah, dibantu, ditangani, follow up, jawab/balas |
| EDU | Product Knowledge (Edukasi) | informasi, penjelasan, dijelaskan, paham, mengerti, informatif, dipandu |
| UMN | Umum / Tanpa Alasan Spesifik | hanya pujian generik tanpa objek |

**Inferensi:** kalau tidak ada keyword touchpoint tapi ada alasan → turunan otomatis:
R5 → EDU · R7 → APP · R8 → TAR · R1–R4/R6 → CS · R9 → UMN.

**Keputusan desain:** produk spesifik menang atas CS (komentar "CS cepat, refund gampang" → REF),
karena yang dicari adalah *obyek* yang membuat user bertahan. Urutan OPS sebelum APP sebelum CS
mengikuti konvensi detractor.

## Hasil snapshot (25 Okt 2026, 2.143 promotor)

**Alasan**
- Respon Cepat & Tanggap 785 (36,6%)
- Tanpa Alasan Spesifik 437 (20,4%)
- Solutif & Tuntas 401 (18,7%)
- Ramah & Empati 197 (9,2%)
- Jelas & Mudah Dipahami 190 (8,9%)
- Mudah & Praktis (Produk) 55 (2,6%)
- Transparan & Amanah 43 (2,0%)
- Proaktif & Follow-up 30 (1,4%)
- Hemat & Promo 5 (0,2%)

→ **1.706 dari 2.143 (80%) promotor menyebut alasan konkret.**

**Touchpoint**
- Layanan CS & Resolusi 1.304 (60,8%)
- Umum / Tanpa Alasan Spesifik 281 (13,1%)
- Aplikasi & Sistem 123 (5,7%)
- Refund & Pembayaran 108 (5,0%)
- Produk Wuzz 98 (4,6%)
- Product Knowledge (Edukasi) 87 (4,1%)
- Membership & Approval 82 (3,8%)
- Petugas & Lokasi 38 (1,8%)
- Produk Bluecharge (EV) 12 (0,6%)
- Tarif & Promo 10 (0,5%)

## Catatan validasi

- Rules engine diuji paritas JS (dashboard) vs Python (CSV): selisih 4 dari 2.143 baris, sudah diperbaiki.
- Sampel manual per bucket (UMN, CS, APP, REF, EDU, MEM) dicek satu per satu; sisa error yang
  diketahui: komentar sangat pendek bernuansa ganda ("pengurusannya cepat" bisa CS atau APP).
- Review per baris: `nps_promoter_reasons_review.csv`
  → http://35.219.2.239:8081/nps_promoter_reasons_review.csv

## ATURAN DATA BARU (standing instruction, 25 Okt 2026)

**Perintah NOOR:** kalau ada data baru dan ada isi komentar + feedback, langsung baca dan masukkan ke kategorinya — tidak perlu nunggu diminta.

Alur yang berlaku:

1. **Klasifikasi otomatis ikut masuk.** Dashboard mengklasifikasi di sisi browser setiap kali data dimuat (`promClassify()`), jadi baris promotor baru **langsung** terhitung di kartu "Promoter per Alasan" tanpa perlu deploy ulang.
2. **Baca komentar baru, bukan cuma andalkan keyword.** Setiap ada data baru masuk, komentar yang belum pernah muncul dibaca ulang satu per satu. Kalau hasil rules meleset, komentar itu masuk daftar override (bukan dibiarkan salah).
3. **Yang perlu perhatian khusus (cek manual dulu):**
   - komentar < 25 karakter yang ambigu (mis. "pengurusannya cepat" → CS atau APP?)
   - komentar campur (puji CS tapi sebut produk lain)
   - komentar dengan typo berat / singkatan lokal (mis. "gercep", "mantul", "sat set")
   - komentar yang isinya keluhan tapi rating promotor (tetap ambil alasan positifnya; kalau tidak ada → `R9`)
4. **Delta report.** Setiap kali sync, laporkan: jumlah promotor baru, sebaran alasan & touchpoint-nya, dan daftar komentar yang butuh verdict NOOR — bukan diam-diam masuk kategori.
5. **Fallback aman.** Kalau ragu, jangan mengarang: masuk `UMN` / `R9` (Umum / Tanpa Alasan Spesifik), lalu tandai untuk review.
6. **Sumber data tetap satu pintu:** Supabase `nps_data` (hasil sync sheet `1lr2WmWJ6oK8zqb2jO0GesX0_OjjRnvizz_6iEM4rg0`). Jangan klasifikasi dari tempat lain.

Tooling: `scripts/nps_promoter_newdata.py` → tarik promotor terbaru, klasifikasi, bandingkan dengan run sebelumnya, keluarkan delta + CSV.

## Cara menambah / mengoreksi

1. Koreksi satu komentar: tambahkan entri di override map (dashboard: `PROM_OVERRIDES`, python: dict serupa)
   dengan key = teks komentar yang sudah dinormalisasi (huruf kecil, tanda baca → spasi).
2. Tambah pola umum: taruh regex baru di `PROM_CAT_RULES` / `PROM_REASON_RULES` pada posisi prioritas yang tepat
   (array dievaluasi dari atas → pertama cocok menang).
3. Setelah mengubah, jalankan ulang `scripts/nps_promoter_classify_v2.py` lalu bandingkan jumlah per kategori
   dengan snapshot di atas — selisih besar = ada regex yang terlalu lebar.
