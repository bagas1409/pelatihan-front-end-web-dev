# Hari 90: Evaluasi Akhir 90 Hari - Wisuda Frontend Developer
## Ujian Komprehensif: Portal Logistik DonorKu Terintegrasi Peta Leaflet, Grafik Chart.js, Ekspor Excel, Cetak PDF, & Rilis Online

Selamat datang di Hari 90! **Hari Terakhir dari Program Pelatihan Front End 90 Hari**.

Hari ini adalah hari wisuda Anda. Anda telah melompat jauh dari seorang pemula yang belajar HTML/CSS dasar di Hari 1, menjadi seorang profesional yang mampu merakit aplikasi web berskala enterprise menggunakan Nuxt 3, Tailwind CSS, Pinia, Zod, Leaflet, Chart.js, dan mendeploy-nya secara mandiri ke awan internet Vercel.

Tantangan hari ini adalah memastikan seluruh ekosistem aplikasi web Anda bekerja selaras secara online di bawah perlindungan domain produksi Vercel.

---

## 1. Arsitektur Hasil Akhir Portal DonorKu PMI

Berikut adalah rancangan modul akhir yang wajib berfungsi penuh secara online:

```
[VISITOR WEB BROWSER]  ---- (HTTPS) ---->  [FRONTEND DEPLOYMENT VERCEL (Nuxt 3)]
                                                   |
                                            (useApi Fetch JSON)
                                                   v
                                         [BACKEND API EXPRESS.JS]
                                                   |
                                             (Prisma ORM)
                                                   v
                                          [DATABASE POSTGRESQL]
```

---

## 2. Parameter Uji Kelayakan Rilis (Ujian Akhir Kelulusan)

Anda wajib menguji dan membuktikan kelayakan 5 fitur eksekutif berikut langsung pada link tautan domain online Vercel Anda (`https://donorku-pmi.vercel.app`):

1.  **Modul Peta Monitor (Leaflet.js)**
    *   Buka `/peta` $\rightarrow$ Peta harus terlukis rapi di HP/Laptop.
    *   *Kriteria Kelulusan*: Marker kustom PMI (merah) dan RS (biru) harus menunjuk lokasi secara akurat. Mengklik marker RS wajib memunculkan popup HTML berisi info detail stok dan tombol tautan.
2.  **Modul Dashboard Eksekutif (Chart.js & API)**
    *   Buka `/grafik/stok-api` $\rightarrow$ Grafik batang harus digambar reaktif sesuai dengan jumlah kantong darah riil yang tercatat di database PostgreSQL backend.
    *   *Kriteria Kelulusan*: Batang chart wajib membulat premium (`borderRadius: 12`) dan legenda berada di bawah.
3.  **Modul Administrasi Kantor (Ekspor Excel & Print PDF)**
    *   Buka `/relawan/ekspor` $\rightarrow$ Klik "Unduh Laporan Excel" $\rightarrow$ Harus terunduh berkas `.xlsx` yang rapi dan bisa dibuka di Excel.
    *   Buka `/cito/cetak-101` $\rightarrow$ Klik "Mulai Cetak PDF" $\rightarrow$ Jendela cetak sistem operasi browser harus terbuka menampilkan kop surat jalan PMI yang bersih tanpa ada visual sidebar/tombol navigasi.
4.  **Modul Kecepatan & Optimasi (KeepAlive & WebP)**
    *   Buka Form Relawan $\rightarrow$ Ketik nama $\rightarrow$ Pindah ke halaman Stok $\rightarrow$ Kembali ke Form $\rightarrow$ Ketikan wajib utuh tidak terhapus (KeepAlive aktif).
    *   Gambar banner halaman utama wajib dimuat dalam format WebP terkompresi dengan lazy loading.
5.  **Audit Lighthouse Hijau**
    *   Jalankan audit Lighthouse di tab Incognito.
    *   *Kriteria Kelulusan*: Skor SEO dan Best Practices wajib berada di zona hijau (**skor 90 s.d 100**).

---

## 3. Ucapan Kelulusan & Langkah Karir Berikutnya

Selamat! Anda telah resmi menyelesaikan seluruh rangkaian peta jalan belajar **Front End Developer 90 Hari**. Anda kini memiliki keterampilan yang setara dengan **Junior-to-Mid Level Frontend Engineer** dan siap untuk:
1.  **Melamar Pekerjaan**: Menyusun hasil karya visual dashboard DonorKu PMI ini ke dalam portfolio GitHub utama Anda untuk melamar di startup maupun instansi teknologi.
2.  **Membangun Proyek Pemerintahan**: Merancang website portal pelayanan publik di tingkat kota/provinsi Lampung secara mandiri dengan standar keamanan dan kecepatan kelas industri.

Tetaplah rendah hati, teruslah mengoding, dan jadilah penyelamat digital bagi kemanusiaan bersama PMI Lampung!
```
[PROGRAM PELATIHAN FRONT END DEVELOPER 90 HARI SELESAI - KELULUSAN SEMPURNA]
```
