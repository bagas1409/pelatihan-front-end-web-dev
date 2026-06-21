# Hari 04: Pengenalan Tailwind CSS
## Memahami Filosofi Utility-First & Cara Setup Tailwind CSS Untuk Proyek Frontend PMI

Selamat datang di Hari 04! Hari ini kita akan berkenalan dengan CSS Framework modern terpopuler di dunia: **Tailwind CSS**.

Jika di Hari 02 dan 03 kita menulis file CSS manual yang panjang dengan menamai kelas sesuka hati (seperti `.kartu-stok`, `.judul-stok`), di Tailwind kita akan merubah total pola pikir tersebut. Kita tidak akan menulis file CSS lagi. Kita akan menghias halaman web langsung di dalam tag HTML menggunakan kelas-kelas utilitas kecil siap pakai (**Utility-First CSS**).

---

## 1. Apa itu Filosofi Utility-First?

### 💡 Analogi Dunia Nyata: "Bermain Balok Lego Warna-warni"
Bayangkan Anda ingin membuat miniatur mainan posko bencana PMI.
*   **CSS Tradisional (Mencetak Mainan Cetakan Pabrik Mandiri)**: Setiap kali Anda butuh pohon, Anda memesan cetakan plastik khusus bernama `Pohon`. Jika butuh genteng, memesan cetakan khusus bernama `Genteng`. Proses ini memakan waktu lama karena Anda harus bolak-balik mendesain cetakan baru (menulis file CSS baru).
*   **Tailwind CSS (Merakit Menggunakan Balok Lego Siap Saji)**: Pabrik lego telah menyediakan ratusan balok kecil berwarna merah, kuning, biru dengan berbagai ukuran panjang-lebar. Untuk membuat posko, Anda tinggal menumpuk balok merah (`bg-red-500`), balok lebar (`w-64`), balok melengkung (`rounded-lg`), dan busa pelindung (`p-4`). 
    *   Anda tidak perlu menciptakan bahan baru. Anda cukup merakit balok-balok kecil yang sudah disediakan pabrik secara instan di tempat.

---

## 2. Keuntungan Menggunakan Tailwind CSS

1.  **Tidak Ada Lagi Kebingungan Menamai Kelas**: Anda tidak perlu pusing memikirkan apakah nama kelas yang tepat `.tabel-stok-darah-baru-utama-ver2` atau cukup `.tabel`.
2.  **Ukuran Berkas Produksi Super Kecil**: Saat di-build untuk dideploy, Tailwind memiliki fitur kompresi pintar (*Purge CSS*). Dia menyisir seluruh tag HTML Anda, mendeteksi kelas lego apa saja yang Anda gunakan, lalu **membuang ribuan kelas lego lain yang tidak terpakai**. Hasil akhirnya ukuran file CSS Anda seringkali hanya berukuran di bawah `10 KB`!
3.  **Desain Konsisten**: Jarak margin, padding, dan pilihan warna sudah dikunci dalam skala rasio baku (misal: `p-4` = `16px`, `p-5` = `20px`). Hal ini mencegah developer menggunakan ukuran acak yang merusak estetika kerapian halaman.

---

## 3. Studi Kasus PMI: Membandingkan Kode CSS Tradisional vs Tailwind

Mari kita bandingkan bagaimana cara membuat tombol peringatan "DOKUMEN CITO DARURAT" yang menyala merah terang.

### Menggunakan CSS Tradisional:
```html
<!-- HTML -->
<button class="tombol-cito">KIRIM CITO</button>

<!-- CSS Terpisah -->
<style>
  .tombol-cito {
    background-color: #ef4444;
    color: white;
    padding: 12px 24px;
    font-weight: bold;
    border-radius: 8px;
    border: none;
    cursor: pointer;
  }
</style>
```

### Menggunakan Tailwind CSS (Utility-First):
```html
<!-- Tanpa perlu menulis file CSS terpisah! -->
<button class="bg-red-500 text-white px-6 py-3 font-bold rounded-lg hover:bg-red-600 transition-colors">
  KIRIM CITO
</button>
```

### Penjelasan Kelas Utilitas Tailwind di Atas:
*   `bg-red-500`: Warna latar belakang merah standar medium.
*   `text-white`: Warna teks putih bersih.
*   `px-6`: Padding horizontal (kiri-kanan) sebesar `24px` (skala 6 = 1.5rem).
*   `py-3`: Padding vertikal (atas-bawah) sebesar `12px` (skala 3 = 0.75rem).
*   `font-bold`: Ketebalan font tebal.
*   `rounded-lg`: Radius kelengkungan sudut kotak sebesar `8px` (large).
*   `hover:bg-red-600`: Saat kursor mouse ditaruh di atas tombol, warna merah berubah menggelap sedikit.
*   `transition-colors`: Memberikan animasi transisi warna yang halus saat warna latar belakang berubah.

---

## 4. Latihan Soal Mandiri
1. Analisis kode tombol Tailwind di atas.
2. Jika Anda ingin mengubah warna tombol tersebut dari merah menjadi biru (misalnya untuk tombol menu "Daftar Relawan Biasa"), sebutkan kelas utilitas Tailwind apa saja yang harus diganti dan tuliskan baris kodenya.
3. Jelaskan apa fungsi dari prefix `hover:` di Tailwind CSS dan bagaimana cara kerjanya di balik layar.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Modifikasi Tombol Menjadi Biru
Untuk merubah tombol menjadi biru, kita cukup mengganti kelas warna latar belakang `bg-red-500` menjadi **`bg-blue-500`**, dan warna efek hover-nya `hover:bg-red-600` menjadi **`hover:bg-blue-600`**.

```html
<button class="bg-blue-500 text-white px-6 py-3 font-bold rounded-lg hover:bg-blue-600 transition-colors">
  DAFTAR RELAWAN
</button>
```
Proses perubahan ini sangat instan karena kita tidak perlu membuka file CSS terpisah, mencari nama kelas, lalu menyuntingnya secara manual.

### Jawaban 3: Properti Hover Tailwind
*   Prefix **`hover:`** adalah cara Tailwind menulis pseudo-class `:hover` CSS secara inline di dalam HTML.
*   **Cara kerja**: Tailwind mendefinisikan kelas CSS khusus di balik layar: `.hover\:bg-blue-600:hover { background-color: rgb(37 99 235); }`. Browser mendeteksi saat pointer mouse bersentuhan fisik dengan elemen tombol tersebut, lalu mengaktifkan warna biru gelap secara otomatis sesuai dengan aturan visual pseudo-class tersebut.
