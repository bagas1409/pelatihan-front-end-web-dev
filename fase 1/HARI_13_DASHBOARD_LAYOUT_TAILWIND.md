# Hari 13: Mendesain Layout Dashboard Admin PMI Pertama dengan Tailwind CSS
## Menyusun Kerangka Halaman Profesional Lengkap dengan Sidebar Kiri, Header Atas, & Area Konten Grid

Selamat datang di Hari 13! Hari ini kita akan menyatukan seluruh ilmu HTML5, CSS Grid, Flexbox, dan Tailwind CSS yang telah dipelajari sejak Hari 1 untuk membangun **Struktur Layout Utama Dashboard Admin Portal DonorKu**.

Layout ini adalah fondasi fisik yang akan kita gunakan sepanjang program pelatihan ini. Kita akan merancang sebuah layout bergaya modern premium menggunakan kombinasi skema warna slate gelap untuk sidebar dan panel putih bersih melayang untuk area kerja admin.

---

## 1. Spesifikasi Rancangan Layout Dashboard Admin
Tata letak kita akan memiliki 3 wilayah utama:
1.  **Sidebar Navigasi (Aside)**: Terletak di sisi kiri layar. Lebar statis `w-64` (256px), warna latar belakang gelap (`bg-slate-900`), berisi daftar menu utama dengan ikon-ikon navigasi.
2.  **Header Informasi (Header)**: Terletak di bagian kanan atas layar. Menempel statis, berisi judul halaman, ringkasan profil petugas, dan tombol pintas log out.
3.  **Area Kerja Konten (Main)**: Sisa area layar bawah kanan. Latar belakang abu-abu terang (`bg-slate-50`), berisi grid-grid kartu data statistik dan tabel transaksi relawan.

---

## 2. Struktur Kode Kerangka Utama (HTML + Tailwind CLI)

Silakan pelajari dan praktikkan penulisan struktur layout berikut:

```html
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>DonorKu Admin - PMI Provinsi Lampung</title>
  <!-- Menghubungkan Tailwind via CDN untuk latihan praktis -->
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-slate-50 font-sans antialiased text-slate-800">

  <!-- WRAPPER UTAMA (FLEXBOX HORIZONTAL) -->
  <div class="flex min-h-screen">

    <!-- 1. SIDEBAR NAVIGASI KIRI -->
    <!-- h-screen -> Mengunci tinggi sidebar setinggi layar monitor penuh -->
    <aside class="w-64 bg-slate-900 text-slate-300 flex flex-col justify-between h-screen sticky top-0">
      
      <!-- Bagian Atas Sidebar (Logo & Nama Aplikasi) -->
      <div>
        <div class="p-6 border-b border-slate-800 flex items-center gap-3">
          <!-- Logo Lingkaran Merah PMI -->
          <div class="w-8 h-8 rounded-full bg-red-500 flex items-center justify-center font-bold text-white text-sm">
            P
          </div>
          <div>
            <h1 class="font-bold text-white text-sm tracking-wide">DonorKu</h1>
            <p class="text-[10px] text-slate-500 uppercase font-semibold">Admin Panel UDD</p>
          </div>
        </div>

        <!-- Daftar Menu Navigasi -->
        <nav class="p-4 space-y-1">
          <a href="#dashboard" class="flex items-center gap-3 px-4 py-3 rounded-xl bg-slate-800 text-white font-medium transition-colors">
            <span>📊</span> Dashboard
          </a>
          <a href="#stok" class="flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-slate-800 hover:text-white transition-colors">
            <span>🩸</span> Kelola Stok
          </a>
          <a href="#relawan" class="flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-slate-800 hover:text-white transition-colors">
            <span>👥</span> Database Relawan
          </a>
          <a href="#cito" class="flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-slate-800 hover:text-white transition-colors">
            <span>🚨</span> Permintaan CITO
          </a>
        </nav>
      </div>

      <!-- Bagian Bawah Sidebar (Info Akun Login) -->
      <div class="p-4 border-t border-slate-800">
        <div class="flex items-center gap-3 p-2 bg-slate-950/40 rounded-xl">
          <div class="w-8 h-8 rounded-full bg-slate-700 flex items-center justify-center text-sm font-bold text-white">
            AS
          </div>
          <div>
            <h4 class="text-xs font-bold text-white leading-tight">dr. Ahmad Syarif</h4>
            <p class="text-[10px] text-slate-500">Super Admin UDD</p>
          </div>
        </div>
      </div>
    </aside>

    <!-- 2. CONTAINER KANAN (HEADER + MAIN CONTENT) -->
    <div class="flex-1 flex flex-col min-w-0">

      <!-- HEADER ATAS (FLEXBOX VERTICAL ALIGNMENT) -->
      <header class="h-16 border-b border-slate-200 bg-white flex items-center justify-between px-8 sticky top-0 z-10">
        <div>
          <h2 class="text-sm font-bold text-slate-900 uppercase tracking-wider">Metrik Ringkasan</h2>
          <p class="text-[10px] text-slate-400">Data ter-update secara berkala dari database pusat UDD</p>
        </div>
        <div class="flex items-center gap-4">
          <!-- Notifikasi Badge Darurat -->
          <div class="relative cursor-pointer">
            <span class="text-xl">🔔</span>
            <span class="absolute -top-1 -right-1 w-4 h-4 bg-red-500 rounded-full text-[9px] text-white flex items-center justify-center font-bold">
              3
            </span>
          </div>
          <button class="text-xs font-bold text-slate-500 hover:text-red-500 transition-colors">
            Logout
          </button>
        </div>
      </header>

      <!-- 3. AREA KONTEN UTAMA (MAIN AREA) -->
      <main class="flex-1 p-8 space-y-6">
        
        <!-- Grid Ringkasan Atas -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm flex justify-between items-center">
            <div>
              <h3 class="text-xs font-bold text-slate-400 uppercase">Total Relawan</h3>
              <p class="text-3xl font-extrabold text-slate-900 mt-2">1,240</p>
            </div>
            <span class="text-3xl">👥</span>
          </div>
          
          <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm flex justify-between items-center">
            <div>
              <h3 class="text-xs font-bold text-slate-400 uppercase">Stok Darah O</h3>
              <p class="text-3xl font-extrabold text-slate-900 mt-2">98 Bag</p>
            </div>
            <span class="text-3xl">🩸</span>
          </div>

          <div class="bg-white p-6 rounded-2xl border border-red-100 bg-red-50/10 shadow-sm flex justify-between items-center">
            <div>
              <h3 class="text-xs font-bold text-red-500 uppercase">Darurat CITO</h3>
              <p class="text-3xl font-extrabold text-red-600 mt-2">2 Kasus</p>
            </div>
            <span class="text-3xl">🚨</span>
          </div>
        </div>

        <!-- Area Detail Tabel Bawah -->
        <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
          <div class="p-6 border-b border-slate-100 flex justify-between items-center">
            <h3 class="font-bold text-slate-900">Agenda Donor Terdekat</h3>
            <button class="bg-red-500 hover:bg-red-600 text-white font-bold text-xs px-4 py-2 rounded-xl transition-colors">
              + Tambah Agenda
            </button>
          </div>
          <div class="p-6 text-center text-slate-400 text-sm">
            [Tabel data riwayat logistik akan dirender dinamis di sini]
          </div>
        </div>

      </main>

    </div>
  </div>

</body>
</html>
```

---

## 3. Latihan Soal Mandiri
1. Analisis kegunaan kelas Tailwind **`sticky top-0`** pada tag `<aside>` dan `<header>` di atas.
2. Apa pengaruhnya terhadap pengalaman kenyamanan petugas admin saat mereka men-scroll ke bawah daftar riwayat tabel relawan yang berisi ribuan baris?
3. Modifikasikan grid metrik ringkasan atas agar bertambah 1 kolom baru (total menjadi 4 kolom) untuk menampilkan metrik **"Persediaan Kantong Kosong"** sebanyak `420 Pcs` dengan ikon kotak kardus `📦`.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Manfaat Properti Sticky
*   **Kegunaan `sticky top-0`**: Berfungsi mengunci posisi elemen agar tetap menempel (membeku) di pojok atas layar monitor meskipun halaman web di-scroll ke arah bawah.
*   **Pengaruh UX**: Sangat membantu kenyamanan petugas admin. Saat mereka men-scroll ribuan baris tabel relawan ke bawah, sidebar navigasi kiri dan header atas tidak akan hilang tergulung ke atas layar. Petugas tetap bisa melihat nama akun mereka, melihat lonceng notifikasi, dan berpindah ke menu lain kapan saja secara instan tanpa perlu repot men-scroll kembali ke baris paling atas halaman (*Zero Scrolling Fatigue*).

### Jawaban 3: Menambah Kolom Ke-4 pada Grid
1.  Ubah kelas grid induk menjadi `md:grid-cols-4` (sebelumnya `md:grid-cols-3`):
    ```html
    <div class="grid grid-cols-1 md:grid-cols-4 gap-6">
    ```
2.  Sisipkan kode kartu ke-4 di dalam grid:
    ```html
    <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm flex justify-between items-center">
      <div>
        <h3 class="text-xs font-bold text-slate-400 uppercase">Kantong Kosong</h3>
        <p class="text-3xl font-extrabold text-slate-900 mt-2">420 Pcs</p>
      </div>
      <span class="text-3xl">📦</span>
    </div>
    ```
    Tampilan dashboard kini akan otomatis membagi grid menjadi 4 kolom sejajar di layar monitor komputer dinas UDD.
