# Hari 27: Nuxt 3 File-Based Routing
## Membuat Peta Navigasi Halaman Otomatis Berdasarkan Struktur Berkas Halaman

Selamat datang di Hari 27! Hari ini kita akan mempelajari salah satu fitur tercanggih Nuxt 3: **File-Based Routing** (Sistem Routing Otomatis berbasis File).

Jika di React JS polos atau Vue polos kita harus menginstal library tambahan (`vue-router` / `react-router-dom`) dan mengetikkan puluhan baris peta rute secara manual di file Javascript (seperti `path: '/stok', component: StokComponent`), di Nuxt 3 kita dibebaskan dari menulis file konfigurasi tersebut. Nuxt bertugas membaca susunan berkas di dalam folder **`pages/`** dan mendaftarkannya menjadi URL situs secara otomatis.

---

## 1. Aturan Pemetaan Folder pages/ menjadi Alamat URL

Nuxt 3 memindai file Anda di folder `pages/` dan menerjemahkannya ke pola alamat web berikut:

| Susunan Berkas di VS Code | Hasil Alamat URL Website | Kategori Halaman |
| :--- | :--- | :--- |
| `pages/index.vue` | `http://localhost:3000/` | Beranda Utama |
| `pages/stok.vue` | `http://localhost:3000/stok` | Kelola Stok Darah |
| `pages/relawan/index.vue` | `http://localhost:3000/relawan` | Database Relawan |
| `pages/relawan/baru.vue` | `http://localhost:3000/relawan/baru` | Tambah Relawan Baru |

---

## 2. Studi Kasus PMI: Membuat Halaman Menu Administrasi UDD

Mari kita buat struktur halaman administrasi UDD PMI Lampung. Ikuti langkah pembuatan berkas berikut di dalam folder proyek Nuxt Anda:

### Berkas 1: Halaman Utama (`pages/index.vue`)
```html
<!-- pages/index.vue -->
<template>
  <div class="p-8 space-y-4">
    <h1 class="text-2xl font-bold text-slate-800">Beranda DonorKu</h1>
    <p class="text-sm text-slate-500">Selamat datang di sistem manajemen UDD PMI Provinsi Lampung.</p>
    <div class="flex gap-4 pt-4">
      <a href="/stok" class="text-red-500 font-bold hover:underline">🩸 Lihat Persediaan Stok</a>
      <a href="/relawan" class="text-blue-500 font-bold hover:underline">👥 Kelola Database Relawan</a>
    </div>
  </div>
</template>
```

### Berkas 2: Halaman Kelola Stok (`pages/stok.vue`)
```html
<!-- pages/stok.vue -->
<template>
  <div class="p-8 space-y-4">
    <h1 class="text-2xl font-bold text-red-500">🩸 Persediaan Kantong Darah</h1>
    <p class="text-sm text-slate-500">Daftar stok darah UDD yang tersimpan di kulkas pendingin medis.</p>
    <a href="/" class="text-xs text-slate-500 hover:underline">&larr; Kembali ke Beranda</a>
  </div>
</template>
```

### Berkas 3: Halaman Induk Relawan (`pages/relawan/index.vue`)
```html
<!-- pages/relawan/index.vue -->
<template>
  <div class="p-8 space-y-4">
    <h1 class="text-2xl font-bold text-slate-800">👥 Database Anggota Relawan</h1>
    <p class="text-sm text-slate-500">Menampilkan daftar seluruh pendonor aktif PMI Lampung.</p>
    <div class="flex gap-4">
      <a href="/relawan/baru" class="text-xs bg-slate-800 text-white px-4 py-2 rounded-xl">
        + Registrasi Baru
      </a>
      <a href="/" class="text-xs text-slate-500 flex items-center hover:underline"> Kembali</a>
    </div>
  </div>
</template>
```

### Berkas 4: Halaman Registrasi Relawan (`pages/relawan/baru.vue`)
```html
<!-- pages/relawan/baru.vue -->
<template>
  <div class="p-8 space-y-4">
    <h1 class="text-xl font-bold text-slate-800">✍️ Registrasi Relawan Baru</h1>
    <p class="text-sm text-slate-500">Masukkan nomor NIK KTP dan golongan darah relawan.</p>
    <a href="/relawan" class="text-xs text-slate-500 hover:underline">&larr; Batal & Kembali</a>
  </div>
</template>
```

Setelah keempat berkas tersebut disimpan, jalankan `npm run dev` dan buka peramban Anda. Ketikkan URL `/stok` atau `/relawan/baru` di kolom browser. Navigasi halaman berjalan lancar seketika tanpa melarikan konfigurasi router tambahan!

---

## 3. Latihan Soal Mandiri
1. Amati tabel pemetaan rute file di atas.
2. Jika Anda membuat file baru bernama `pages/laporan/tahunan.vue`, sebutkan di alamat URL manakah browser bisa mengakses isi dari halaman laporan tahunan tersebut.
3. Sebutkan mengapa file halaman di Nuxt 3 wajib memiliki hanya **satu elemen pembungkus root** (seperti dibungkus tag `<div>` besar) di bagian `<template>`-nya.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Peta URL Laporan Tahunan
*   Alamat URL aksesnya adalah: **`http://localhost:3000/laporan/tahunan`**
*   **Alasan**: Nuxt membaca folder bersarang `laporan` sebagai rute induk dinamis tingkat pertama, dan nama file `tahunan.vue` sebagai anak sub-rute tingkat kedua di bawahnya.

### Jawaban 3: Aturan Single Root Element
*   Meskipun Vue 3 Composition API secara teknis mendukung rendering multi-root (Fragment), Nuxt 3 sangat merekomendasikan dan mewajibkan halaman pembuka memiliki **1 elemen pembungkus root terluar** (misal dibungkus `<main>` atau `<div>` utama).
*   **Alasan**: Hal ini disebabkan sistem transisi halaman otomatis Nuxt (`pageTransition`) dan optimasi Server-Side Rendering (SSR) membutuhkan jangkar elemen DOM fisik tunggal yang solid untuk melakukan animasi masuk-keluar element dan memetakan proses hidrasi data secara akurat tanpa memicu error *Hydration Mismatch*.
