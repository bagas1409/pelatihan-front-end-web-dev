# Hari 05: Desain Layout Responsif dengan Tailwind CSS
## Menyusun Tata Letak Grid Fleksibel yang Menyesuaikan Diri Otomatis di HP & Monitor PC Dinas

Selamat datang di Hari 05! Hari ini kita akan mempelajari cara merancang antarmuka yang adaptif (**Responsif**) menggunakan sistem breakpoint Tailwind CSS.

Di era mobile, website admin PMI tidak hanya dibuka lewat komputer meja (PC desktop) kantor UDD. Petugas lapangan di lokasi bencana seringkali membuka admin panel tersebut menggunakan HP Android berspesifikasi rendah, sementara pimpinan memantau grafik stok lewat tablet iPad. Kita akan belajar cara membuat tata letak yang otomatis menata ulang dirinya sendiri agar selalu pas di semua ukuran layar.

---

## 1. Konsep Breakpoint di Tailwind CSS

Tailwind CSS menggunakan pendekatan **Mobile-First** (Prioritas Seluler). Artinya:
*   Semua kelas yang Anda tulis tanpa awalan breakpoint (seperti `w-full`, `grid-cols-1`) akan diterapkan secara standar pada **layar ukuran terkecil (HP)**.
*   Jika layar bertambah lebar melewati batas piksel tertentu, Anda menyisipkan prefix khusus (seperti `md:`, `lg:`) untuk merubah aturan layout tersebut.

### Daftar Ukuran Layar Standar Tailwind:
*   `(tanpa prefix)` $\rightarrow$ Layar HP / Mobile (Lebar di bawah `640px`).
*   **`sm:`** $\rightarrow$ Layar HP besar / Lanskap (Lebar `640px` ke atas).
*   **`md:`** $\rightarrow$ Layar Tablet (Lebar `768px` ke atas).
*   **`lg:`** $\rightarrow$ Layar Laptop / PC Monitor (Lebar `1024px` ke atas).
*   **`xl:`** $\rightarrow$ Layar Monitor Desktop Lebar (Lebar `1280px` ke atas).

---

## 2. Studi Kasus PMI: Grid Metrik Informasi Responsif

Kita ingin membuat halaman dashboard ringkasan stok darah yang:
*   Di **HP**: Menampilkan kartu stok berbaris menurun ke bawah (**1 kolom**) agar mudah di-scroll satu jempol.
*   Di **Tablet**: Menampilkan kartu berbaris menyamping (**2 kolom**).
*   Di **Laptop/PC**: Menampilkan kartu berbaris penuh (**4 kolom**) agar seluruh informasi terlihat sekaligus tanpa perlu scroll.

### Contoh Kode HTML Tailwind:

```html
<!-- Container Pembungkus Dashboard -->
<div class="p-6 bg-slate-100 min-h-screen">
  
  <!-- JUDUL DENGAN UKURAN TEKS DINAMIS -->
  <!-- HP: text-lg (18px), Tablet ke atas: text-2xl (24px) -->
  <h2 class="text-lg md:text-2xl font-bold text-slate-800 mb-6">
    Ringkasan Persediaan UDD Lampung
  </h2>

  <!-- GRID RESPONSIF CERDAS -->
  <!-- 
    Default (HP): grid-cols-1 (1 kolom)
    md (Tablet): md:grid-cols-2 (2 kolom)
    lg (Laptop): lg:grid-cols-4 (4 kolom)
  -->
  <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
    
    <!-- KARTU GOLONGAN A -->
    <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
      <h3 class="text-slate-500 font-semibold text-xs uppercase tracking-wider">Golongan A</h3>
      <p class="text-3xl font-extrabold text-slate-900 mt-2">12 Kantong</p>
      <span class="text-xs text-emerald-600 font-medium block mt-2">✓ Stok Aman</span>
    </div>

    <!-- KARTU GOLONGAN B -->
    <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
      <h3 class="text-slate-500 font-semibold text-xs uppercase tracking-wider">Golongan B</h3>
      <p class="text-3xl font-extrabold text-slate-900 mt-2">45 Kantong</p>
      <span class="text-xs text-emerald-600 font-medium block mt-2">✓ Stok Aman</span>
    </div>

    <!-- KARTU GOLONGAN AB -->
    <div class="bg-white p-6 rounded-2xl border border-red-200 bg-red-50/20 shadow-sm">
      <h3 class="text-red-500 font-semibold text-xs uppercase tracking-wider">Golongan AB</h3>
      <p class="text-3xl font-extrabold text-red-600 mt-2">4 Kantong</p>
      <span class="text-xs text-red-600 font-medium block mt-2">🚨 Kritis! Segera CITO</span>
    </div>

    <!-- KARTU GOLONGAN O -->
    <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
      <h3 class="text-slate-500 font-semibold text-xs uppercase tracking-wider">Golongan O</h3>
      <p class="text-3xl font-extrabold text-slate-900 mt-2">98 Kantong</p>
      <span class="text-xs text-emerald-600 font-medium block mt-2">✓ Stok Aman</span>
    </div>

  </div>

</div>
```

---

## 3. Latihan Soal Mandiri
1. Amati kode kelas pada container grid di atas: `class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6"`.
2. Jika sebuah laptop dinas dengan lebar resolusi layar `1150px` membuka halaman web tersebut, berapakah jumlah kolom kartu yang akan digambar di layar laptop tersebut? Jelaskan breakpoint mana yang aktif mendominasi.
3. Sebutkan cara menyembunyikan sebuah komponen Sidebar (`<aside>`) secara total di layar HP, namun menampilkannya kembali secara otomatis saat web dibuka di layar Tablet/PC menggunakan kelas utilitas Tailwind.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Analisis Breakpoint Layar 1150px
*   Jumlah kolom yang akan digambar adalah **4 Kolom**.
*   **Alasan**: Lebar layar `1150px` telah melewati batas minimal breakpoint **`lg:`** (yaitu `1024px`). Di dalam sistem Tailwind Mobile-First, aturan dengan spesifikasi breakpoint tertinggi yang cocok dengan lebar layar saat ini yang akan menang mendominasi rute CSS. Aturan `lg:grid-cols-4` menimpa `md:grid-cols-2` dan `grid-cols-1`.

### Jawaban 3: Menyembunyikan Komponen Secara Responsif
Untuk menyembunyikan sidebar di HP dan menampilkannya kembali di layar yang lebih lebar (mulai tablet/PC), kita mengombinasikan kelas **`hidden`** (sembunyi mutlak) dengan breakpoint **`md:block`** (tampilkan kembali sebagai elemen block).

```html
<!-- 
  Di HP: hidden (tidak dirender sama sekali)
  Di Tablet ke atas (md:): block (diberi display block sehingga muncul kembali)
-->
<aside class="hidden md:block w-64 bg-slate-900 text-white min-h-screen">
  <!-- Isi menu navigasi sidebar -->
</aside>
```
Aturan ini sangat umum dipakai pada web admin modern untuk menyembunyikan sidebar navigasi kiri dan menggantinya dengan tombol menu burger (hamburger menu) melayang saat dibuka di layar handphone.
