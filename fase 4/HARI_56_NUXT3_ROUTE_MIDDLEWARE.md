# Hari 56: Nuxt 3 Route Middleware - Proteksi Rute
## Mengamankan Halaman Dashboard Agar Tidak Bisa Diakses Sebelum Login Petugas

Selamat datang di Hari 56! Hari ini kita memulai **Fase 4**. Di fase ini kita akan fokus pada sistem otentikasi middleware, pengerjaan CRUD kompleks relawan, penanganan token kadaluarsa, polling real-time, dan manipulasi upload file gambar. 

Materi hari ini membahas cara membuat filter pengaman jalan masuk halaman: **Route Middleware** (Middleware Rute) di Nuxt 3. Kita akan memastikan halaman rahasia dashboard logistik PMI tidak bisa diintip oleh orang luar yang belum memiliki token JWT login dinas.

---

## 1. Konsep Route Middleware

### 💡 Analogi Dunia Nyata: "Pos Satpam Gerbang Masuk UDD PMI"
Bayangkan kantor UDD PMI Lampung dijaga ketat oleh pos satpam di pintu depan.
*   **Tanpa Middleware (Pintu Terbuka Bebas)**: Siapapun bisa masuk langsung ke ruang laboratorium logistik darah tanpa diperiksa KTP-nya. Ini sangat rawan kejahatan.
*   **Dengan Middleware (Pemeriksaan Satpam)**: Setiap kali ada orang berjalan melangkahkan kaki menuju gedung (`Pages Transition`), Satpam menghentikan mereka di pintu gerbang. Satpam bertanya: *"Tolong tunjukkan kartu pengenal dinas Anda."*
    *   Jika kartu pengenal valid terdeteksi (Token JWT di Pinia ada), Satpam membukakan pintu dan mempersilakan masuk (`return`).
    *   Jika kartu pengenal kosong (Token `null`), Satpam memegang pundak orang tersebut, melarang masuk, dan menyeretnya kembali ke luar pos satpam (melakukan redirect otomatis ke halaman `/login`).

---

## 2. Struktur Menulis Middleware di Nuxt 3

Nuxt 3 secara otomatis memindai seluruh file yang terletak di dalam folder **`middleware/`** root proyek Anda. 
Setiap berkas wajib mengekspor fungsi **`defineNuxtRouteMiddleware((to, from))`** di mana:
*   `to`: Berisi info rute halaman tujuan yang ingin dibuka oleh pengguna.
*   `from`: Berisi info rute halaman asal tempat pengguna mengklik link.

---

## 3. Studi Kasus PMI: Membuat Auth Middleware (`middleware/auth.js`)

1.  Buat folder baru bernama **`middleware`** di root direktori proyek Nuxt Anda.
2.  Buat file baru di dalamnya bernama **`auth.js`** dan isi dengan kode filter berikut:

```javascript
// middleware/auth.js
import { useAuthStore } from '~/stores/auth';

export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore();

  // Cek apakah token JWT kosong di Pinia/Cookie
  if (!authStore.token) {
    console.warn("Akses ilegal terdeteksi! Mengalihkan pengunjung ke halaman login.");
    
    // Kembalikan helper navigateTo untuk membelokkan arah jalan secara paksa ke login
    return navigateTo('/login');
  }
});
```

---

## 4. Cara Memasang Middleware di Halaman `pages/`

Kita tinggal memanggil nama file middleware tersebut (tanpa ekstensi `.js`) ke dalam properti `middleware` di dalam helper **`definePageMeta`** pada halaman yang ingin diamankan.

### Contoh Berkas Halaman Stok (`pages/stok.vue`):
```html
<!-- pages/stok.vue -->
<template>
  <div class="p-8">
    <h2 class="font-bold">Halaman Stok UDD Rahasia</h2>
    <p class="text-xs">Hanya petugas dinas yang sudah login bisa membaca tabel ini.</p>
  </div>
</template>

<script setup>
definePageMeta({
  layout: 'default',
  // Daftarkan middleware pengaman 'auth'
  middleware: 'auth' // Sesuai nama berkas "middleware/auth.js"
});
</script>
```

Sekarang coba buka peramban Anda dan ketik paksa URL `/stok` secara manual saat Anda belum melakukan login. Browser akan secara instan memantulkan halaman Anda kembali ke `/login` dalam milidetik!

---

## 5. Latihan Soal Mandiri
1. Amati parameter `to` dan `from` pada fungsi middleware di atas.
2. Jelaskan bahaya keamanan bagi data rahasia PMI jika kita hanya mengandalkan proteksi middleware di frontend tanpa diiringi dengan proteksi token JWT di tingkat database server backend Express.js.
3. Sebutkan nama properti konfigurasi jika kita ingin agar suatu middleware berjalan secara otomatis di **seluruh** rute halaman website tanpa perlu didaftarkan satu per satu di file halaman.

---

## 6. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Keterbatasan Keamanan Frontend
*   **Bahaya Bypass**: Middleware rute di frontend Nuxt 3 murni bertugas mengamankan keindahan visual antarmuka pengguna di browser. Seseorang yang berniat jahat (Hacker) bisa dengan mudah menonaktifkan Javascript di browser mereka, meretas state memori RAM Vue, atau langsung menembak API backend Express.js kita menggunakan aplikasi Postman.
*   **Aturan Keamanan Utama**: Keamanan sejati terletak di backend. Database backend Express.js Anda **wajib selalu memverifikasi validitas token JWT** di setiap request HTTP header. Frontend hanya bertugas menyelaraskan navigasi visual agar petugas tidak kebingungan.

### Jawaban 3: Global Middleware
*   Untuk membuat middleware berjalan otomatis menyelimuti seluruh rute tanpa deklarasi manual, kita cukup menambahkan sufiks kata **`.global`** pada nama file berkasnya.
*   *Contoh*: Jika nama file diubah menjadi **`middleware/auth.global.js`**, maka setiap kali pengguna berpindah ke halaman manapun di website, satpam global akan langsung memeriksa token dinasnya.
