# Hari 18: Struktur Direktori Nuxt 4
## Memahami Aturan Penempatan Berkas Pages, Components, Layouts, & Public di Dalam Folder app/

Selamat datang di Hari 18! Hari ini kita akan menjelajahi peta wilayah dan **Struktur Direktori (Susunan Folder)** di dalam proyek Nuxt 4.

Berbeda dengan Nuxt 3 yang meletakkan folder `pages/`, `components/`, `layouts/`, dsb di tingkat paling luar (root), **Nuxt 4 memperkenalkan struktur yang lebih bersih dengan menyatukan seluruh kode sumber aplikasi frontend ke dalam folder `app/`**. 

Nuxt 4 menerapkan konsep **Convention over Configuration** (Kesepakatan Aturan di atas Konfigurasi). Nama-nama folder khusus di dalam `app/` bersifat otomatis terbaca. Jika kita menaruh file di dalam folder tersebut dengan nama yang tepat, Nuxt akan secara otomatis merakit fitur di balik layar (seperti routing instan dan auto-import).

---

## 1. Peta Folder Utama Nuxt 4

### 💡 Analogi Dunia Nyata: "Tata Ruang Puskesmas Cabang PMI"
Bayangkan Anda memasuki gedung pusat UDD PMI yang tertata rapi. Seluruh aktivitas medis berada di dalam **Gedung Utama (Folder `app/`)**:
*   **Folder `app/pages/` (Poli Kamar Periksa)**: Setiap pintu kamar diberi nama (Poli Gigi, Poli UGD). Pengunjung berjalan mencari ruang berdasarkan papan nama pintu. (Menyusun alamat URL website).
*   **Folder `app/components/` (Gudang Alat Medis Reusable)**: Berisi jarum suntik, tensimeter, tandu yang bisa dibawa ke kamar periksa manapun secara fleksibel tanpa perlu memesan baru. (Komponen visual reusable seperti Button/Card).
*   **Folder `app/layouts/` (Tema Seragam Staf)**: Seragam resmi jas putih dokter atau rompi merah relawan. Siapapun yang memakai seragam ini langsung terbungkus rapi visualnya. (Layout pembungkus kerangka web).
*   **Folder `public/` (Kotak Brosur di Parkiran Luar - Tetap di Root)**: Berisi selebaran kertas foto brosur yang bebas diambil siapapun langsung dari luar gedung tanpa perlu izin masuk resepsionis. (Aset statis gambar logo, favicon, atau file pdf).

---

## 2. Rincian Fungsi Direktori Penting

Berikut adalah direktori utama yang wajib Anda ketahui saat membangun portal DonorKu dengan Nuxt 4:

1.  **`app/app.vue`**
    *   Gerbang masuk pertama aplikasi Nuxt. File ini bertindak sebagai induk utama dari semua halaman. Di Nuxt 4, berkas ini dipindahkan ke dalam folder `app/`.
2.  **`app/pages/`**
    *   *Sangat penting!* Di sinilah rute halaman web Anda dibuat. Jika Anda membuat file `app/pages/stok.vue`, Nuxt otomatis mendaftarkan alamat URL: `http://localhost:3000/stok`.
3.  **`app/components/`**
    *   Berisi komponen Vue. Hebatnya, semua file komponen di folder ini otomatis di-impor oleh Nuxt. Anda bisa langsung memanggil komponen tersebut di halaman mana saja tanpa perlu menulis baris kode `import` manual!
4.  **`app/layouts/`**
    *   Mengatur kerangka layout halaman. Sangat berguna untuk memisahkan layout halaman Login (tanpa sidebar) dengan layout halaman Dashboard Admin (dengan sidebar kiri).
5.  **`public/` (Tetap di Root)**
    *   Berisi file statis (gambar, robot.txt, favicon). File di dalam folder ini disajikan langsung di alamat root. Jika ada file `public/logo-pmi.png`, kita bisa memanggilnya langsung di HTML dengan alamat src: `/logo-pmi.png`.
6.  **`nuxt.config.ts` (Tetap di Root)**
    *   Pusat kendali pengaturan konfigurasi aplikasi Nuxt 4.

---

## 3. Studi Kasus PMI: Mengubah app/app.vue untuk Membaca Routing Halaman

Secara default, saat pertama kali proyek dibuat, file `app/app.vue` berisi kode selamat datang statis. Kita harus mengubahnya agar `app/app.vue` bersedia membaca dan merender file halaman dinamis dari folder `app/pages/` menggunakan tag **`<NuxtPage />`**.

### Langkah 1: Sunting Berkas `app/app.vue`
Buka file `app/app.vue` di folder `app/` proyek Anda, lalu ubah isinya menjadi sesederhana ini:

```html
<!-- app/app.vue -->
<template>
  <div class="antialiased text-slate-800">
    <!-- Tag ajaib Nuxt yang bertugas merender halaman dinamis dari folder app/pages/ -->
    <NuxtPage />
  </div>
</template>
```

### Langkah 2: Buat Folder `app/pages/` & File Halaman Pertama
1.  Buat folder baru bernama **`app/pages`** (jika belum ada).
2.  Buat file di dalamnya bernama **`index.vue`** (merupakan beranda utama `/`):
    ```html
    <!-- app/pages/index.vue -->
    <template>
      <div class="p-8 text-center">
        <h1 class="text-3xl font-bold text-red-500">Selamat Datang di DonorKu</h1>
        <p class="text-slate-600 mt-2">Portal Database UDD PMI Lampung</p>
      </div>
    </template>
    ```
3.  Simpan dan buka browser Anda. Halaman index tersebut otomatis menggantikan logo selamat datang Nuxt secara instan!

---

## 4. Latihan Soal Mandiri
1. Analisis isi file `app/app.vue` yang baru saja diubah di atas.
2. Jelaskan apa perbedaan utama lokasi penempatan berkas `app.vue` dan folder `pages/` antara Nuxt 3 dengan Nuxt 4.
3. Sebutkan kelebihan fitur *Auto-Imports* komponen pada Nuxt 4 dibanding cara tradisional mengimpor komponen di React JS.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Perbedaan Lokasi Berkas Nuxt 3 vs Nuxt 4
*   **Di Nuxt 3**: Berkas `app.vue` dan folder `pages/` diletakkan langsung di direktori root terluar proyek.
*   **Di Nuxt 4**: Berkas `app.vue` dipindahkan ke dalam folder `app/` (yaitu `app/app.vue`), begitu pula dengan folder `pages/` (yaitu `app/pages/`). Ini memisahkan file kode sumber (src) dari berkas konfigurasi root (seperti `package.json`, `nuxt.config.ts`, `tailwind.config.js`) sehingga struktur direktori root menjadi sangat bersih dan rapi.
*   Jika tag `<NuxtPage />` dihapus dari file `app/app.vue`, browser tidak akan pernah merender halaman-halaman yang kita buat di dalam folder `app/pages/`.

### Jawaban 3: Keunggulan Auto-Imports Komponen
*   **Di React JS (Kuno/Manual)**: Setiap kali ingin memanggil komponen tombol `<Button />`, kita harus menulis baris impor di bagian atas: `import Button from '../components/Button.jsx'`. Jika kita memindahkan posisi folder file, link path impor tersebut rawan pecah (error path).
*   **Di Nuxt 4 (Otomatis)**: Nuxt memantau isi folder `app/components/` secara terus-menerus. Kita bisa langsung mengetikkan `<Button />` atau `<HeaderBar />` di dalam template HTML mana saja secara instan tanpa menulis satu baris pun kode `import` di tag script. Hal ini menghemat ribuan baris kode impor dan mempercepat waktu coding developer secara signifikan.
