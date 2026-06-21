# Hari 52: Menampilkan Notifikasi Melayang (Toast)
## Mengintegrasikan vue3-toastify Sebagai Plugin & Memberikan Umpan Balik Sukses-Gagal Premium

Selamat datang di Hari 52! Hari ini kita akan mempraktikkan penginstalan modul notifikasi melayang: **`vue3-toastify`** menggunakan sistem plugin Nuxt 3 yang sudah dipelajari di Hari 51.

Saat kita berhasil mendaftarkan relawan baru atau mendeteksi error server di Hari 48, kita hanya menampilkan peringatan menggunakan fungsi bawaan browser yang kaku (`alert()`). Hari ini kita akan menggantinya dengan notifikasi balon melayang (**Toast Notification**) yang memiliki animasi meluncur, icon dinamis, dan warna premium khas antarmuka modern.

---

## 1. Langkah Penginstalan & Registrasi Plugin

### Langkah 1: Instal Library Vue3-Toastify
Jalankan perintah berikut di terminal root proyek Anda:
```bash
npm install vue3-toastify
```

### Langkah 2: Buat Berkas Plugin Client (`plugins/toastify.client.js`)
Buat file bernama **`toastify.client.js`** di dalam folder `plugins/` (wajib menggunakan ekstensi `.client` karena library ini memanipulasi elemen visual layar browser):

```javascript
// plugins/toastify.client.js
import Vue3Toastify, { toast } from 'vue3-toastify';
import 'vue3-toastify/dist/index.css'; // Impor berkas CSS styles bawaannya!

export default defineNuxtPlugin((nuxtApp) => {
  // Pasang library ke dalam instance aplikasi Vue
  nuxtApp.vueApp.use(Vue3Toastify, {
    autoClose: 3000, // Notifikasi menutup otomatis setelah 3 detik
    position: 'top-right', // Muncul di pojok kanan atas layar monitor
    theme: 'colored'
  });

  // Ekspos fungsi "toast" ke global agar bisa dipanggil di mana saja
  return {
    provide: {
      toast
    }
  };
});
```

---

## 2. Studi Kasus PMI: Memicu Toast Notifikasi di Halaman Form

Setelah didaftarkan, kita bisa memanggil fungsi `$toast` di halaman mana saja tanpa import. Mari kita modifikasi halaman registrasi relawan di `pages/relawan/baru.vue` agar memicu toast.

```html
<template>
  <div class="max-w-xs mx-auto p-8 bg-white rounded-3xl border shadow-sm mt-8 space-y-4">
    <h3 class="font-bold text-slate-800 text-sm">Demo Uji Coba Toast</h3>
    
    <div class="flex flex-col gap-2">
      <button 
        @click="tampilkanSukses" 
        class="bg-emerald-500 text-white font-bold py-2 rounded-xl text-xs"
      >
        Simpan Data (Sukses)
      </button>

      <button 
        @click="tampilkanGagal" 
        class="bg-red-500 text-white font-bold py-2 rounded-xl text-xs"
      >
        Simpan Data (Gagal)
      </button>
    </div>
  </div>
</template>

<script setup>
// Ambil fungsi $toast dari nuxtApp global
const { $toast } = useNuxtApp();

const tampilkanSukses = () => {
  // Pemicu toast sukses melayang
  $toast.success("✓ Sukses menyimpan data relawan ke database UDD!", {
    position: 'top-right',
  });
};

const tampilkanGagal = () => {
  // Pemicu toast gagal melayang
  $toast.error("🚨 Gagal memproses data! NIK KTP sudah terdaftar.", {
    position: 'top-right',
  });
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati parameter konfigurasi: `autoClose: 3000` dan `theme: 'colored'` pada file plugin di atas.
2. Jelaskan fungsi dari parameter `autoClose` dan tuliskan apa yang harus diset jika kita ingin agar notifikasi toast tersebut tetap terus menempel di layar dan tidak boleh menutup otomatis sampai diklik silang oleh petugas admin.
3. Sebutkan mengapa file plugin di atas wajib diberi nama berkas berekstensi `.client.js` dan apa dampaknya jika kita lupa menuliskan kata `.client` tersebut.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Konfigurasi Toast Auto Close
*   **Fungsi `autoClose: 3000`**: Mengatur durasi waktu tunda (dalam satuan milidetik / 3 detik) sebelum balon notifikasi meluncur menyembunyikan diri dari layar.
*   **Menolak Tutup Otomatis**: Jika kita ingin agar notifikasi darurat CITO terus menempel di layar sampai diklik manual oleh petugas medis, kita menyuplai nilai boolean **`false`** pada properti `autoClose`:
    ```javascript
    $toast.error("🚨 Kritis: Segera konfirmasi!", { autoClose: false });
    ```

### Jawaban 3: Kegunaan Ekstensi Client untuk Toast
*   **Alasan Wajib `.client.js`**: Library `vue3-toastify` bertugas memanipulasi pohon DOM HTML fisik dengan membuat elemen kotak peringatan melayang yang dinamis. 
*   **Akibat jika dihapus**: Jika kata `.client` dihapus, server Node.js Nuxt (SSR) akan mencoba merender kode CSS dan script toastify tersebut. Karena di server tidak ada layar monitor fisik (*Window & Document Object*), server akan melempar pesan error dan merusak proses pemuatan halaman utama website.
