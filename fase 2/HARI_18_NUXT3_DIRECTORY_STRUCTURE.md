# Hari 18: Struktur Direktori Nuxt 3
## Memahami Aturan Penempatan Berkas Pages, Components, Layouts, & Public

Selamat datang di Hari 18! Hari ini kita akan menjelajahi peta wilayah dan **Struktur Direktori (Susunan Folder)** di dalam proyek Nuxt 3.

Berbeda dengan menulis web React/Vue polos yang membebaskan kita menaruh file di folder mana saja, Nuxt 3 menerapkan konsep **Convention over Configuration** (Kesepakatan Aturan di atas Konfigurasi). Nuxt 3 memiliki nama-nama folder khusus yang bersifat keramat. Jika kita menaruh file di dalam folder tersebut dengan nama yang tepat, Nuxt akan secara otomatis merakit fitur di balik layar secara ajaib (seperti routing instan dan auto-import).

---

## 1. Peta Folder Utama Nuxt 3

### 💡 Analogi Dunia Nyata: "Tata Ruang Puskesmas Cabang PMI"
Bayangkan Anda memasuki puskesmas PMI yang tertata rapi. Setiap barang ditaruh di ruangan khusus dengan aturan ketat:
*   **Folder `pages/` (Poli Kamar Periksa)**: Setiap pintu kamar diberi nama (Poli Gigi, Poli UGD). Pengunjung berjalan mencari ruang berdasarkan papan nama pintu. (Menyusun alamat URL website).
*   **Folder `components/` (Gudang Alat Medis Reusable)**: Berisi jarum suntik, tensimeter, tandu yang bisa dibawa ke kamar periksa manapun secara fleksipan tanpa perlu memesan baru. (Komponen visual reusable seperti Button/Card).
*   **Folder `layouts/` (Tema Seragam Staf)**: Seragam resmi jas putih dokter atau rompi merah relawan. Siapapun yang memakai seragam ini langsung terbungkus rapi visualnya. (Layout pembungkus kerangka web).
*   **Folder `public/` (Kotak Brosur di Parkiran Luar)**: Berisi selebaran kertas foto brosur yang bebas diambil siapapun langsung dari luar gedung tanpa perlu izin masuk resepsionis. (Aset statis gambar logo, favicon, atau file pdf).

---

## 2. Rincian Fungsi Direktori Penting

Berikut adalah direktori utama yang wajib Anda ketahui saat membangun portal DonorKu:

1.  **`app.vue`**
    *   Gerbang masuk pertama aplikasi Nuxt. File ini bertindak sebagai induk utama dari semua halaman.
2.  **`pages/`**
    *   *Sangat penting!* Di sinilah rute halaman web Anda dibuat. Jika Anda membuat file `pages/stok.vue`, Nuxt otomatis mendaftarkan alamat URL: `http://localhost:3000/stok`.
3.  **`components/`**
    *   Berisi komponen Vue. Hebatnya, semua file komponen di folder ini otomatis di-impor oleh Nuxt. Anda bisa langsung memanggil komponen tersebut di halaman mana saja tanpa perlu menulis baris kode `import` manual!
4.  **`layouts/`**
    *   Mengatur kerangka layout halaman. Sangat berguna untuk memisahkan layout halaman Login (tanpa sidebar) dengan layout halaman Dashboard Admin (dengan sidebar kiri).
5.  **`public/`**
    *   Berisi file statis (gambar, robot.txt, favicon). File di dalam folder ini disajikan langsung di alamat root. Jika ada file `public/logo-pmi.png`, kita bisa memanggilnya langsung di HTML dengan alamat src: `/logo-pmi.png`.
6.  **`nuxt.config.ts`**
    *   Pusat kendali pengaturan konfigurasi aplikasi Nuxt 3.

---

## 3. Studi Kasus PMI: Mengubah app.vue untuk Membaca Routing Halaman

Secara default, saat pertama kali proyek dibuat, file `app.vue` berisi kode selamat datang statis. Kita harus mengubahnya agar `app.vue` bersedia membaca dan merender file halaman dinamis dari folder `pages/` menggunakan tag **`<NuxtPage />`**.

### Langkah 1: Sunting Berkas `app.vue`
Buka file `app.vue` di root proyek Anda, lalu ubah isinya menjadi sesederhana ini:

```html
<!-- app.vue -->
<template>
  <div class="antialiased text-slate-800">
    <!-- Tag ajaib Nuxt yang bertugas merender halaman dinamis dari folder pages/ -->
    <NuxtPage />
  </div>
</template>
```

### Langkah 2: Buat Folder `pages/` & File Halaman Pertama
1.  Buat folder baru bernama **`pages`** di direktori utama Anda.
2.  Buat file di dalamnya bernama **`index.vue`** (merupakan beranda utama `/`):
    ```html
    <!-- pages/index.vue -->
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
1. Analisis isi file `app.vue` yang baru saja diubah di atas.
2. Jelaskan apa dampak visual bagi website admin kita jika kita menghapus tag `<NuxtPage />` di dalam berkas `app.vue` tersebut.
3. Sebutkan kelebihan fitur *Auto-Imports* komponen pada Nuxt 3 dibanding cara tradisional mengimpor komponen di React JS.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya NuxtPage
*   Jika tag `<NuxtPage />` dihapus dari file `app.vue`, browser **tidak akan pernah merender** halaman-halaman yang kita buat di dalam folder `pages/` (seperti `index.vue`, `stok.vue`, dll). Layar browser akan menampilkan halaman kosong (blank) putih total karena gerbang masuk utama kehilangan instruksi untuk memanggil dan merender sistem rute dinamis Nuxt.

### Jawaban 3: Keunggulan Auto-Imports Komponen
*   **Di React JS (Kuno/Manual)**: Setiap kali ingin memanggil komponen tombol `<Button />`, kita harus menulis baris impor di bagian atas: `import Button from '../components/Button.jsx'`. Jika kita memindahkan posisi folder file, link path impor tersebut rawan pecah (error path).
*   **Di Nuxt 3 (Otomatis)**: Nuxt memantau isi folder `components/` secara terus-menerus. Kita bisa langsung mengetikkan `<Button />` atau `<HeaderBar />` di dalam template HTML mana saja secara instan tanpa menulis satu baris pun kode `import` di tag script. Hal ini menghemat ribuan baris kode impor dan mempercepat waktu coding developer secara signifikan.
