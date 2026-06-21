# Hari 53: SEO & Meta Tags Dinamis
## Mengatur Title, Deskripsi, & Open Graph Menggunakan useHead & useSeoMeta

Selamat datang di Hari 53! Hari ini kita akan mempelajari cara mengoptimalkan **SEO (Search Engine Optimization)** secara dinamis menggunakan composable bawaan Nuxt 3: **`useHead()`** dan **`useSeoMeta()`**.

Saat kita membuat halaman detail relawan atau info stok UDD PMI, kita ingin agar jika link website tersebut dibagikan ke WhatsApp atau media sosial, muncul kartu pratinjau yang cantik berisi logo PMI, judul halaman yang sesuai, dan deskripsi ringkasan yang menarik. Kita juga ingin Google dengan mudah merayap dan membaca data halaman kita.

---

## 1. Perbedaan: useHead vs useSeoMeta

*   **`useHead()` (Konfigurasi Head Tag Umum)**:
    *   Digunakan untuk menyuntikkan tag-tag HTML dasar di dalam area `<head>` seperti mengganti `<title>`, mengubah bahasa `<html lang="id">`, menyisipkan favicon ikon kustom, atau memuat file skrip JavaScript/font Google Fonts eksternal.
*   **`useSeoMeta()` (Konfigurasi Khusus Mesin Pencari & Sosial Media)**:
    *   Menggunakan penulisan objek JavaScript datar (Flat Object) khusus untuk mengisi meta tag pencarian Google dan tag **Open Graph (og:title, og:description)** milik WhatsApp/Facebook/Twitter, menjamin visual preview link tampil premium.

---

## 2. Studi Kasus PMI: Set Title & Meta Tag Dinamis Halaman Detail Relawan

Mari kita pasang optimasi SEO dinamis pada file halaman detail relawan kita di `pages/relawan/[id].vue` agar meta tag-nya berubah sesuai nama relawan yang sedang aktif dibuka.

```html
<!-- pages/relawan/[id].vue -->
<template>
  <div class="max-w-md mx-auto p-6 bg-white rounded-3xl border mt-8">
    <h3 class="font-bold text-slate-800 text-base">Profil Pendonor</h3>
    
    <div v-if="relawan" class="mt-4 space-y-2 text-sm">
      <p>Nama: <strong>{{ relawan.nama }}</strong></p>
      <p>Golongan Darah: <strong>{{ relawan.gol }}</strong></p>
    </div>
  </div>
</template>

<script setup>
import { useRoute } from 'vue-router';
import { ref } from 'vue';

const route = useRoute();
const id = route.params.id;

// Simulasi data relawan dari API database
const relawan = ref({
  nama: 'Budi Santoso',
  gol: 'O',
  region: 'Bandar Lampung'
});

// 1. MENGATUR TAG HTML HEAD UMUM
useHead({
  htmlAttrs: {
    lang: 'id' // Mengatur bahasa situs web menjadi Bahasa Indonesia
  },
  link: [
    {
      rel: 'icon',
      type: 'image/x-icon',
      href: '/favicon-pmi.ico' // Favicon logo PMI di folder public/
    }
  ]
});

// 2. MENGATUR META TAG SEO & SOSIAL MEDIA SECARA DINAMIS
useSeoMeta({
  // Judul halaman browser berubah dinamis menyertakan nama relawan
  title: `Profil Pendonor: ${relawan.value.nama} - DonorKu UDD`,
  
  description: `Detail keanggotaan relawan PMI Lampung atas nama ${relawan.value.nama} bergolongan darah ${relawan.value.gol}.`,
  
  // Tag Open Graph untuk WhatsApp / Facebook Share Preview Card
  ogTitle: `Profil Pendonor: ${relawan.value.nama} - PMI Lampung`,
  ogDescription: `Ayo donor darah bersama PMI Lampung. Cek status keaktifan kartu anggota donor di sini.`,
  ogImage: 'https://lampung.pmi.or.id/wp-content/uploads/2021/logo-pmi.png', // Logo PMI pratinjau
  ogUrl: `https://donorku.pmi.org/relawan/${id}`,
  
  // Tag Twitter Card
  twitterCard: 'summary_large_image',
  twitterTitle: `Profil Pendonor: ${relawan.value.nama}`
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati kode pengaturan SEO di atas: `title: \`Profil Pendonor: \${relawan.value.nama}\``.
2. Jelaskan apa akibat buruknya bagi performa mesin pencari Google (*Search Indexing*) jika kita membiarkan semua halaman web kita memiliki title tag statis yang sama (misal: `<title>Portal UDD PMI</title>`) tanpa pernah diubah secara dinamis.
3. Sebutkan di direktori folder manakah kita harus menaruh berkas gambar favicon `favicon-pmi.ico` agar bisa dibaca langsung oleh browser menggunakan tautan `href: '/favicon-pmi.ico'`.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Bahaya Duplikasi Title Tag untuk SEO
*   **Bahaya Duplikasi**: Mesin pencari Google menyukai keunikan struktur data. Jika 1.000 halaman profil relawan yang berbeda semuanya memiliki judul title tag yang sama persis (`Portal UDD PMI`), robot perayap Google (*Googlebot*) akan menganggap halaman-halaman tersebut sebagai **konten duplikat (Duplicate Content Bug)**.
*   **Dampaknya**: Google akan menyembunyikan halaman-halaman tersebut dari hasil pencarian, menurunkan skor SEO situs web PMI Lampung, dan membuat pengurus kesulitan mencari profil relawan via kata kunci nama di Google. Judul yang unik (`Profil Pendonor: Budi Santoso`) membantu identifikasi rute secara akurat.

### Jawaban 3: Penempatan File Aset Statis
*   **Folder Tujuan**: Kita harus menaruh berkas `favicon-pmi.ico` tepat di dalam folder **`public/`** di root proyek Nuxt 3 (contoh: `public/favicon-pmi.ico`).
*   **Alasan**: Folder `public` disajikan oleh server Nuxt secara polos tanpa proses kompilasi bundler Webpack/Vite. Seluruh file di dalamnya langsung diletakkan di root server web. Folder ini sangat pas untuk menaruh file statis seperti robot.txt, favicon, berkas PDF formulir medis, atau logo utama instansi.
