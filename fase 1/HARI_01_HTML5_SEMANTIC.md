# Hari 01: Anatomi HTML5 & Semantic Web
## Memahami Pentingnya Struktur Halaman yang Bersih & Mudah Dibaca Mesin pada Portal PMI

Selamat datang di Hari 01! Kita akan memulai petualangan belajar frontend dengan membangun fondasi paling dasar dari setiap halaman web di internet: **HTML5**.

Di web modern, membuat struktur halaman yang hanya bermodalkan tag `<div>` dan `<span>` di semua tempat adalah cara lama yang buruk. Kita akan mempelajari **HTML5 Semantic Web** — tag khusus yang memiliki makna arti bagi browser dan mesin pencari (SEO Google) demi aksesibilitas dan kemudahan pengelolaan data PMI.

---

## 1. Apa itu Semantic HTML?

### 💡 Analogi Dunia Nyata: "Denah Peta Gedung Kantor PMI"
Bayangkan Anda adalah arsitek yang merancang gedung baru PMI Lampung. Anda membuat gambar denah cetak biru:
*   **Tanpa Semantik (Hanya Kotak Kosong)**: Anda menggambar 10 kotak kosong berukuran sama di atas kertas tanpa tulisan apa pun. Orang yang melihat denah (seperti Google search bot atau pembaca layar tunanetra) tidak akan tahu kotak mana yang merupakan *Ruang Unit Gawat Darurat (UGD)*, *Gudang Obat*, atau *Resepsionis Depan*.
*   **Dengan Semantik (Label Ruangan Jelas)**: Anda menandai kotak atas sebagai **`Lobby/Header`**, kotak samping sebagai **`Lorong Navigasi/Aside`**, kotak tengah besar sebagai **`Gedung Utama/Main`**, dan kotak bawah sebagai **`Pintu Keluar/Footer`**. Pengunjung langsung paham tata letak gedung dalam 1 detik.

Semantic HTML adalah pemberian label makna yang jelas pada setiap area halaman web Anda.

---

## 2. Struktur Tag Semantic Utama di HTML5

Berikut adalah tag-tag penting untuk menyusun kerangka Dashboard Admin DonorKu:

1.  **`<header>`**: Area kepala halaman web. Biasanya berisi logo PMI, kolom pencarian, nama profil admin, dan notifikasi.
2.  **`<nav>`**: Kumpulan tautan navigasi. Digunakan khusus untuk menu link berpindah halaman.
3.  **`<aside>`**: Konten samping (Sidebar). Sangat pas untuk menu navigasi kiri dashboard admin.
4.  **`<main>`**: Konten utama halaman. Di sinilah area kerja kita menampilkan data tabel stok darah dan grafik statistik.
5.  **`<section>`**: Bagian kelompok konten yang saling berhubungan di dalam area utama (misal: bagian rekap stok, bagian tabel log transaksi).
6.  **`<article>`**: Konten mandiri yang bisa berdiri sendiri (seperti postingan pengumuman agenda donor darah keliling).
7.  **`<footer>`**: Kaki halaman web, biasanya berisi hak cipta (copyright) dan link syarat ketentuan PMI.

---

## 3. Studi Kasus PMI: Merancang Kerangka Kasar Web Dashboard

Mari kita buat struktur HTML5 murni yang merepresentasikan rancangan dashboard admin DonorKu.

### Kode HTML Contoh:

```html
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <title>Dashboard Admin DonorKu - PMI Lampung</title>
</head>
<body>

    <!-- KEPALA HALAMAN -->
    <header>
        <h1>PMI PROVINSI LAMPUNG</h1>
        <p>Sistem Informasi Logistik UDD & Database Relawan</p>
    </header>

    <!-- CONTAINER UTAMA -->
    <div style="display: flex;">

        <!-- MENU NAVIGASI SAMPING (SIDEBAR) -->
        <aside>
            <nav>
                <ul>
                    <li><a href="#dashboard">Dashboard Utama</a></li>
                    <li><a href="#stok">Kelola Stok Darah</a></li>
                    <li><a href="#relawan">Database Relawan</a></li>
                    <li><a href="#cito">Permintaan CITO</a></li>
                </ul>
            </nav>
        </aside>

        <!-- KONTEN UTAMA DASHBOARD -->
        <main>
            <section id="metrik-stok">
                <h2>Ringkasan Persediaan Hari Ini</h2>
                <article>
                    <h3>Stok Darah Golongan O</h3>
                    <p>Persediaan: <strong>120 Kantong</strong> (Status: Aman)</p>
                </article>
                <article>
                    <h3>Stok Darah Golongan A</h3>
                    <p>Persediaan: <strong>8 Kantong</strong> (Status: Kritis!)</p>
                </article>
            </section>
        </main>

    </div>

    <!-- KAKI HALAMAN -->
    <footer>
        <p>&copy; 2026 UDD PMI Provinsi Lampung. Hak Cipta Dilindungi.</p>
    </footer>

</body>
</html>
```

---

## 4. Latihan Soal Mandiri
1. Amati struktur kode HTML di atas.
2. Mengapa kita tidak disarankan membungkus tautan menu navigasi di dalam tag `<aside>` saja tanpa menyisipkan tag `<nav>`? Jelaskan manfaat tag `<nav>` bagi alat pembaca suara tunanetra (*Screen Reader*).
3. Buatlah struktur HTML5 semantik sederhana khusus untuk halaman **"Detail Pengumuman Agenda Donor Keliling"** yang berisi judul agenda, lokasi UDD penyelenggara, dan artikel berita agenda tersebut.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2:
Tag `<aside>` berfungsi memberi tahu browser bahwa area tersebut adalah konten samping (pendukung). Namun, mesin pembaca layar (*Screen Reader*) untuk tunanetra tidak akan mendeteksi secara otomatis bahwa di dalam area samping tersebut terdapat deretan link menu utama jika kita tidak membungkusnya dengan tag `<nav>`. Tag `<nav>` memberi sinyal kepada sistem aksesibilitas browser untuk membacakan pesan suara: *"Memasuki menu navigasi utama aplikasi"*, mempermudah penyandang disabilitas bernavigasi tanpa melihat layar.

### Jawaban 3: Struktur Halaman Detail Agenda:
```html
<main>
    <!-- Section khusus agenda -->
    <section>
        <header>
            <h1>Agenda Donor Darah Sukarela Ramadhan</h1>
            <p>Diselenggarakan oleh: UDD PMI Kota Bandar Lampung</p>
        </header>

        <article>
            <p>Kegiatan ini akan dilangsungkan di Mall Boemi Kedaton lantai dasar mulai pukul 16.00 WIB. Dapatkan bingkisan menarik bagi 100 pendonor pertama.</p>
        </article>

        <footer>
            <p>Kategori: Donor Keliling | Kuota: 150 Peserta</p>
        </footer>
    </section>
</main>
```
Dengan struktur ini, Google Bot akan dengan sangat mudah merayap dan menampilkan berita agenda donor ini di halaman utama pencarian Google karena struktur datanya sangat logis dan rapi.
