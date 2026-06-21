# Hari 89: Deployment Frontend Nuxt 3 ke Vercel
## Langkah Demi Langkah Rilis Produksi Online & Konfigurasi Environment Variables API Server

Selamat datang di Hari 89! Hari ini kita akan mempraktikkan proses rilis online nyata pertama kita dengan mendeploy aplikasi frontend Nuxt 3 ke server **Vercel** secara gratis.

Kita juga akan mempelajari cara menyetel **Environment Variables** (Variabel Lingkungan) di server produksi. Selama mengoding di laptop lokal, kita menembak server backend lokal `http://localhost:5000`. Setelah website online di Vercel, kita harus menginstruksikan server Vercel agar menembak API server awan asli, seperti `https://pmi-backend-api.onrender.com/api` secara aman tanpa memperlihatkan kodenya di internet.

---

## 1. Aliran Setup Git & Vercel (Panduan Langkah-langkah)

### Langkah 1: Hubungkan Proyek ke GitHub
1.  Buat repositori baru di akun GitHub Anda (beri nama: `donorku-frontend`).
2.  Push kode proyek lokal Anda ke GitHub:
    ```bash
    git init
    git add .
    git commit -m "feat: siap rilis produksi"
    git branch -M main
    git remote add origin https://github.com/akun-anda/donorku-frontend.git
    git push -u origin main
    ```

### Langkah 2: Buat Akun & Sambungkan Vercel
1.  Buka situs **[Vercel](https://vercel.com/)** $\rightarrow$ Klik **Sign Up** $\rightarrow$ Pilih **Continue with GitHub** (untuk menyambungkan kredensial akun GitHub Anda secara instan).
2.  Setelah masuk ke Dashboard Vercel, klik tombol **Add New...** $\rightarrow$ Pilih **Project**.
3.  Di daftar repositori GitHub yang muncul, temukan repositori `donorku-frontend` $\rightarrow$ Klik tombol **Import**.

### Langkah 3: Konfigurasi Variabel API (Environment Variables)
Sebelum mengklik tombol deploy, kita harus memasukkan variabel API di halaman konfigurasi Vercel:
1.  Temukan menu accordion bertuliskan **Environment Variables**.
2.  Masukkan data penunjuk arah API Anda:
    *   **Key**: `NUXT_PUBLIC_API_URL`
    *   **Value**: `https://pmi-backend-api.onrender.com/api` (Tautan API backend asli Anda)
3.  Klik tombol **Add**.

### Langkah 4: Klik Deploy!
1.  Biarkan konfigurasi Framework Preset terdeteksi otomatis sebagai **Nuxt.js**.
2.  Klik tombol **Deploy**.
3.  Tunggu sekitar 1 s.d 2 menit selagi robot Vercel merakit kode Anda di server awan. Setelah selesai, kembang api virtual akan meledak di layar monitor dan Vercel akan memberikan domain gratis online Anda, contoh: `https://donorku-frontend.vercel.app`.

---

## 2. Cara Membaca Variabel Lingkungan di Kode Nuxt 3

Agar kode Javascript kita di laptop bisa membaca variabel `NUXT_PUBLIC_API_URL` yang disuntikkan oleh Vercel di atas, kita wajib mendaftarkannya di dalam konfigurasi **`runtimeConfig`** di file `nuxt.config.ts`:

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  compatibilityDate: '2024-04-03',
  modules: [
    '@nuxtjs/tailwindcss'
  ],
  
  // DAFTARKAN RUNTIMECONFIG
  runtimeConfig: {
    public: {
      // Nilai default ini digunakan jika variabel env di laptop kosong (development)
      apiUrl: process.env.NUXT_PUBLIC_API_URL || 'http://localhost:5000/api'
    }
  }
})
```

Cara memanggil URL API tersebut di dalam file custom fetch **`composables/useApi.js`** kita:
```javascript
// composables/useApi.js
export const useApi = (path, options = {}) => {
  // Ambil runtimeConfig Nuxt 3
  const config = useRuntimeConfig();
  
  // Baca apiUrl secara aman dan dinamis!
  const BASE_URL = config.public.apiUrl;

  return useFetch(`${BASE_URL}${path}`, {
    ...options
  });
};
```

---

## 3. Latihan Soal Mandiri
1. Amati kode konfigurasi `runtimeConfig` di atas.
2. Jelaskan mengapa kita dilarang keras menuliskan alamat URL API server backend cloud (`https://pmi-backend-api.onrender.com/api`) secara mentah (*Hardcoded*) di dalam file kode JavaScript biasa, dan jelaskan hubungannya dengan rahasia keamanan server.
3. Sebutkan apa kelebihan dari penggunaan prefix kata **`public`** di dalam objek `runtimeConfig` di atas bagi aksesibilitas data di sisi browser client.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya RuntimeConfig & Keamanan Data
*   **Mencegah Kebocoran Informasi (Secret Leakage)**: Menuliskan URL server backend secara mentah di file JavaScript akan terekam selamanya di dalam riwayat commit Git. Jika repositori GitHub Anda bersifat publik, siapa saja bisa menyalin alamat server database Anda dan menyerangnya. 
*   **Kemudahan Konfigurasi**: Dengan runtimeConfig, kita memisahkan **Kode Program** dengan **Konfigurasi Lingkungan**. Kita bisa menggunakan kode JavaScript yang sama persis di laptop (menembak localhost) dan di Vercel (menembak production server) tanpa perlu mengedit isi file kode program satu barispun.

### Jawaban 3: Kegunaan RuntimeConfig Public Object
*   **Akses Browser Client**: Properti variabel yang dimasukkan ke dalam sub-objek **`public`** dipastikan bisa dibaca oleh script JavaScript baik di sisi server Node.js maupun di sisi browser client (*Universal Access*).
*   Jika kita sengaja menaruh properti di luar objek `public` (misalnya menaruh langsung di bawah `runtimeConfig: { rahasiaDatabase: '...' }`), Nuxt akan melindunginya secara ketat dan **hanya memperbolehkan file di sisi server Node.js yang membacanya**, mencegah data sensitif tersebut bocor terkirim ke browser peramban pengguna biasa.
