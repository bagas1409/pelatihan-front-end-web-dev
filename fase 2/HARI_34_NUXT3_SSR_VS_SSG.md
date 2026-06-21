# Hari 34: Server-Side Rendering (SSR) vs Static Site Generation (SSG)
## Memahami Cara Nuxt 3 Menyajikan Halaman Web & Pengaruhnya Terhadap Kecepatan Load Medis

Selamat datang di Hari 34! Hari ini kita akan mempelajari perbedaan dua mode penyajian halaman web terpopuler di Nuxt 3: **Server-Side Rendering (SSR)** dan **Static Site Generation (SSG)**.

Memilih mode rendering yang tepat sangat menentukan seberapa cepat website dinas PMI Lampung bisa dibuka di daerah yang minim sinyal internet, dan seberapa mudah mesin pencari Google membaca daftar stok darah kita untuk ditampilkan di halaman pertama pencarian.

---

## 1. Konsep Dasar Mode Rendering

### 💡 Analogi Dunia Nyata: "Dapur Rumah Sakit: Prasmanan Siap Saji vs Koki Memasak Sesuai Orderan"
*   **SSG (Prasmanan Siap Saji - Static Site Generation)**:
    *   Sebelum gerbang UDD dibuka pagi hari, koki sudah memasak 100 piring nasi goreng dan menaruhnya di atas meja. Ketika pengunjung masuk dan meminta nasi goreng, petugas langsung menyerahkan piring tersebut dalam waktu 1 detik. Pengunjung tidak perlu menunggu proses memasak.
    *   *Kelemahan*: Nasi goreng tidak bisa disesuaikan rasanya di tempat. Jika pengunjung meminta tambahan telur setengah matang secara mendadak, piring tidak bisa dirubah instan karena sudah terlanjur dimasak matang sejak subuh. (Cocok untuk halaman statis: Profil PMI, Syarat Donor, Alamat Cabang).
*   **SSR (Koki Memasak Sesuai Orderan - Server-Side Rendering)**:
    *   Pengunjung memesan bubur ayam khusus tanpa garam. Koki baru mengambil wajan, memotong bahan, memasaknya di dapur belakang, lalu menyerahkannya hangat-hangat setelah 5 menit.
    *   *Keunggulan*: Data masakan selalu baru sesuai pesanan detik itu juga. (Cocok untuk halaman dinamis: Tabel Stok Darah UDD yang terus berkurang tiap menit, Permintaan CITO).

---

## 2. Perbandingan Teknis SSR vs SSG di Nuxt 3

### A. Server-Side Rendering (SSR - Default Nuxt 3)
*   **Cara Kerja**: Ketika browser meminta alamat `/stok`, server Node.js Nuxt akan mengambil data terbaru dari database, merakitnya menjadi file HTML matang di server, lalu mengirimkan HTML matang tersebut ke browser.
*   **Keunggulan**: SEO sangat bagus karena Google Bot langsung menerima halaman berisi data teks riil stok, bukan halaman kosong.
*   **Kekurangan**: Server Node.js harus terus aktif menyala 24 jam dan memproses kompilasi HTML di setiap klik pengunjung.

### B. Static Site Generation (SSG - Static Mode)
*   **Cara Kerja**: Saat kita menjalankan perintah build `npx nuxi generate`, Nuxt akan merayap seluruh rute halaman, mengambil data API saat itu juga, lalu mengubah seluruh website menjadi file HTML/CSS statis murni yang mati.
*   **Keunggulan**: File statis bisa diunggah ke server gratisan (seperti Netlify atau GitHub Pages) yang sangat murah dan cepat dibuka di HP karena tidak ada proses database di server saat diklik.
*   **Kekurangan**: Jika ada relawan mendaftar donor baru di database, halaman web tidak akan ter-update visualnya sampai kita melakukan proses build ulang (*re-generate*) dari awal.

---

## 3. Konfigurasi Mode Rendering di `nuxt.config.ts`

Nuxt 3 sangat hebat karena mendukung **Hybrid Rendering** (Menggabungkan keduanya sekaligus di satu aplikasi). Kita bisa mengatur rute mana yang disajikan sebagai SSR dan mana yang disajikan sebagai SSG di berkas konfigurasi `nuxt.config.ts`.

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  compatibilityDate: '2024-04-03',
  devtools: { enabled: true },
  modules: ['@nuxtjs/tailwindcss'],

  // Tentukan aturan rendering per alamat URL (Hybrid Rendering)
  routeRules: {
    // Halaman Beranda UDD dimasak di server secara real-time (SSR)
    '/': { ssr: true },
    
    // Halaman Stok Darah di-render murni di browser client saja (SPA Mode / No SSR)
    '/stok': { ssr: false },
    
    // Halaman Statis Profil PMI dibuat mati sekali sejak awal build (SSG)
    '/profil-pmi': { prerender: true },
  }
})
```

---

## 4. Latihan Soal Mandiri
1. Amati konfigurasi `routeRules` di atas.
2. Jika Anda membuat portal portal berita PMI Lampung yang artikel beritanya hanya ditulis sekali seminggu oleh staf Humas, sebutkan mode rendering manakah yang paling efisien dari segi biaya server dan kecepatan akses pembaca. Jelaskan alasannya.
3. Apa perbedaan utama antara Client-Side Rendering (SPA / `ssr: false`) dengan Server-Side Rendering (`ssr: true`) dalam hal apa yang diterima oleh browser saat pertama kali mengunduh file index web?

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pemilihan Mode untuk Berita PMI
*   Mode rendering terbaik adalah **SSG (Static Site Generation / `prerender: true`)**.
*   **Alasan**: Karena artikel berita jarang sekali berubah (hanya sekali seminggu), kita tidak perlu membebani server Node.js untuk merakit HTML berulang kali di setiap kunjungan pembaca. Dengan memprerender halaman menjadi berkas statis sekali seminggu, halaman berita tersebut akan dimuat secepat kilat (di bawah 100ms) di handphone pembaca, menghemat kuota internet relawan, dan sangat mudah di-indeks oleh mesin Google pencari berita.

### Jawaban 3: Perbedaan SSR vs CSR (SPA) di Awal Muat
*   **Client-Side Rendering (CSR / SPA)**: Browser pertama kali mengunduh file HTML kosong: `<body><div id="app"></div></body>`. Browser kemudian mengunduh file Javascript raksasa, mengeksekusinya, memanggil API, baru menggambar tampilannya. Google Bot seringkali hanya membaca halaman kosong ini.
*   **Server-Side Rendering (SSR)**: Browser langsung menerima HTML matang yang sudah terisi paragraf teks dan tabel stok darah lengkap dari server sejak milidetik pertama. Browser hanya bertugas menyegarkan tali pengikat reaktif (*Hydration*). Layar langsung terbaca lengkap seketika tanpa jeda memuat skrip JS terlebih dahulu.
