# Hari 57: Guest Middleware
## Mencegah Petugas yang Sudah Login Membuka Ulang Halaman Login

Selamat datang di Hari 57! Hari ini kita akan mempelajari teknik proteksi arah sebaliknya: **Guest Middleware** (Middleware Khusus Tamu) via berkas **`middleware/guest.js`** di Nuxt 3.

Jika kemarin kita bertugas menendang keluar user asing ke halaman login, hari ini kita harus memecahkan masalah UX yang mengganggu: jika seorang petugas UDD sudah sukses login dan berada di dashboard, lalu ia tidak sengaja memencet tombol Back atau mengetik manual URL `/login` di peramban, ia tidak boleh disuguhi form login kosong lagi. Dia harus dipantulkan kembali ke halaman dalam dashboard secara otomatis.

---

## 1. Kegunaan Guest Middleware

*   **Auth Middleware (`auth.js`)**: Hanya mengizinkan masuk orang yang **sudah login**. (Target: halaman dashboard `/stok`, `/relawan`).
*   **Guest Middleware (`guest.js`)**: Hanya mengizinkan masuk orang yang **belum login / tamu asing**. Jika mereka terdeteksi sudah memiliki token di memori, mereka akan langsung dialihkan masuk ke dalam dashboard. (Target: halaman `/login`, `/register`).

---

## 2. Studi Kasus PMI: Membuat Guest Middleware (`middleware/guest.js`)

1.  Buat file baru di folder `middleware/` Anda bernama **`guest.js`**.
2.  Isi dengan kode logika pemeriksaan berikut:

```javascript
// middleware/guest.js
import { useAuthStore } from '~/stores/auth';

export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore();

  // Jika token terdeteksi ada di memori (sudah login)
  if (authStore.token) {
    console.info("User sudah login aktif. Mengalihkan ke dashboard utama.");
    
    // Pantulkan kembali secara paksa ke beranda utama
    return navigateTo('/');
  }
});
```

---

## 3. Cara Memasang Guest Middleware pada Halaman Login

Buka berkas halaman login Anda di **`pages/login.vue`** dan daftarkan middleware tamu tersebut menggunakan `definePageMeta`:

```html
<!-- pages/login.vue -->
<template>
  <div class="text-center space-y-4">
    <h2 class="text-lg font-bold">Login Dinas PMI</h2>
    <p class="text-xs text-slate-400">Silakan masukkan username petugas</p>
    <!-- ... form login ... -->
  </div>
</template>

<script setup>
definePageMeta({
  // Gunakan layout minimalis auth
  layout: 'auth',
  // Daftarkan middleware tamu 'guest'
  middleware: 'guest' // Sesuai nama berkas "middleware/guest.js"
});
</script>
```

Sekarang jalankan uji coba: login ke aplikasi dashboard, lalu ketik manual alamat URL `http://localhost:3000/login` di kolom navigasi browser. Anda akan mendeteksi browser memantulkan Anda kembali ke halaman beranda `/` secara instan!

---

## 4. Latihan Soal Mandiri
1. Amati alur pemeriksaan `if (authStore.token)` di atas.
2. Jika suatu website memiliki halaman pendaftaran relawan baru yang boleh diisi oleh siapa saja (baik tamu yang belum login maupun admin yang sudah login), sebutkan middleware mana yang paling cocok ditempelkan di halaman tersebut.
3. Sebutkan kelebihan pemisahan logika `auth` dan `guest` ke dalam dua berkas file terpisah dibandingkan menyatukannya menjadi baris kode `if-else` raksasa di satu file middleware global.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pemilihan Halaman Bebas
*   **Pilihan Terbaik**: Halaman tersebut **tidak perlu dipasang middleware apapun (Tanpa Middleware)**.
*   **Alasan**: Jika dipasang `auth.js`, tamu asing tidak akan bisa mendaftar donor karena ditendang ke halaman login. Jika dipasang `guest.js`, petugas admin tidak akan bisa membantu mendaftarkan relawan dari meja pendaftaran UDD karena admin ditendang ke dashboard. Halaman publik/hibrida harus dibiarkan bersih dari middleware agar bisa diakses bebas.

### Jawaban 3: Keunggulan Modular Middleware
*   **Keterbacaan Kode (Readability)**: Menghindari terciptanya struktur spageti kode yang rumit di mana satu file middleware sibuk membaca 10 baris array `to.path` hanya untuk mencocokkan rute mana yang boleh dibuka tamu dan mana yang boleh dibuka admin.
*   **Modular & Deklaratif**: Cukup dengan membaca baris paling atas di `<script setup>` tiap halaman (`middleware: 'auth'` atau `middleware: 'guest'`), developer baru langsung mengerti kebijakan privasi akses halaman tersebut tanpa perlu membuka file lain.
