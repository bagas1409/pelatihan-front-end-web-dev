# Hari 03: CSS Grid & Flexbox Layouting
## Menyusun Struktur Grid Kartu Persediaan Darah UDD Agar Rapi Sejajar & Responsif

Selamat datang di Hari 03! Hari ini kita akan mempelajari dua senjata tata letak terkuat dalam CSS modern: **Flexbox** dan **CSS Grid**.

Sebelum adanya kedua fitur ini, developer web harus menggunakan trik aneh (seperti `float` atau `position absolute`) untuk menyejajarkan menu samping dan deretan kotak kartu data, yang seringkali berantakan saat dibuka di HP. Flexbox dan Grid merevolusi hal tersebut dengan membawa sistem tata letak 1 dimensi dan 2 dimensi yang cerdas.

---

## 1. Perbedaan Utama: Flexbox vs CSS Grid

### 💡 Analogi Dunia Nyata: "Gerbong Kereta vs Rak Lemari Kaca"
*   **Flexbox (1 Dimensi - Gerbong Kereta)**: Flexbox berfokus pada penyusunan baris elemen ke **satu arah saja** (mendatar horizontal saja, atau menurun vertikal saja).
    *   *Analogi*: Seperti menyusun gerbong kereta api logistik PMI. Anda menyambungkan gerbong satu per satu ke belakang. Jika gerbong terlalu panjang, gerbong bisa otomatis meliput ke bawah rel cadangan jika disetujui. Flexbox sangat cocok untuk komponen kecil seperti barisan tombol menu atas (*Navbar*) atau susunan tombol filter.
*   **CSS Grid (2 Dimensi - Rak Lemari Kaca Apotek)**: Grid berfokus pada penyusunan elemen ke **dua arah sekaligus** secara bersamaan (Horizontal Kolom & Vertikal Baris).
    *   *Analogi*: Seperti merancang rak lemari etalase kaca tempat menaruh botol obat di PMI. Anda merancang lemari yang memiliki 3 kolom vertikal dan 4 baris horizontal. Anda bisa menyuruh satu kotak obat besar memakan 2 kolom sekaligus. Grid sangat cocok untuk kerangka layout halaman induk (*Main Dashboard Layout*).

---

## 2. Mengenal Properti Kunci Flexbox
Untuk mengaktifkan Flexbox pada elemen pembungkus (Parent):
*   `display: flex;` $\rightarrow$ Mengaktifkan mode flexbox.
*   `flex-direction: row | column;` $\rightarrow$ Arah barisan (mendatar atau menurun).
*   `justify-content: flex-start | center | space-between;` $\rightarrow$ Mengatur jarak horizontal antar item anak.
*   `align-items: center | stretch;` $\rightarrow$ Mengatur kelurusan vertikal item anak.

---

## 3. Studi Kasus PMI: Grid Persediaan Golongan Darah & Flex Navbar

Kita akan merancang layout halaman web PMI Lampung yang memiliki:
1.  **Header Navbar** atas menggunakan **Flexbox** agar tombol profil sejajar di pojok kanan.
2.  **Grid Stok Darah** bawah menggunakan **CSS Grid** agar 4 kartu golongan darah (A, B, AB, O) berbaris rapi 4 kolom sejajar.

### Kode HTML & CSS Lengkap:

```html
<!DOCTYPE html>
<html lang="id">
<head>
    <style>
        body { font-family: sans-serif; background-color: #f8fafc; margin: 0; padding: 20px; }

        /* 1. NAVBAR (MENGGUNAKAN FLEXBOX) */
        .navbar {
            display: flex;
            justify-content: space-between; /* Dorong judul ke kiri, profil ke kanan */
            align-items: center; /* Kelurusan vertikal agar teks sejajar di tengah */
            background-color: #ffffff;
            padding: 15px 24px;
            border-radius: 12px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.05);
            margin-bottom: 24px;
        }
        .profil-admin {
            display: flex;
            align-items: center;
            gap: 10px; /* Jarak kosong antara ikon lingkaran dan nama */
        }
        .avatar { width: 32px; height: 32px; background-color: #ef4444; border-radius: 50%; }

        /* 2. GRID STOK DARAH (MENGGUNAKAN CSS GRID) */
        .grid-stok {
            display: grid;
            /* Merancang 4 kolom sejajar berukuran sama besar (1 fraction / 1fr) */
            grid-template-columns: repeat(4, 1fr); 
            gap: 20px; /* Jarak pembatas antar kotak kartu */
        }

        .kartu {
            background-color: #ffffff;
            padding: 24px;
            border-radius: 16px;
            border: 1px solid #e2e8f0;
            text-align: center;
        }
        .huruf-gol { font-size: 28px; font-weight: 900; margin: 0; color: #1e293b; }
        .teks-jumlah { color: #64748b; font-size: 14px; margin-top: 8px; }
    </style>
</head>
<body>

    <!-- NAVBAR FLEXBOX -->
    <header class="navbar">
        <h2 style="margin: 0; font-size: 18px; color: #0f172a;">Portal DonorKu</h2>
        <div class="profil-admin">
            <div class="avatar"></div>
            <span style="font-weight: bold; font-size: 13px;">Admin UDD Lampung</span>
        </div>
    </header>

    <!-- GRID AREA 4 KOLOM -->
    <main class="grid-stok">
        <div class="kartu">
            <h3 class="huruf-gol">A</h3>
            <p class="teks-jumlah">12 Kantong</p>
        </div>
        <div class="kartu">
            <h3 class="huruf-gol">B</h3>
            <p class="teks-jumlah">45 Kantong</p>
        </div>
        <div class="kartu">
            <h3 class="huruf-gol">AB</h3>
            <p class="teks-jumlah">9 Kantong</p>
        </div>
        <div class="kartu">
            <h3 class="huruf-gol">O</h3>
            <p class="teks-jumlah">98 Kantong</p>
        </div>
    </main>

</body>
</html>
```

---

## 4. Latihan Soal Mandiri
1. Analisis kode `grid-template-columns: repeat(4, 1fr);` di atas.
2. Jika Anda membuka halaman web tersebut di HP yang layarnya sempit, sebutkan kelemahan visual apa yang akan terjadi pada susunan kartu stok darah jika kita memaksakan pembagian 4 kolom (`1fr`).
3. Sebutkan properti CSS Grid alternatif yang bisa kita pasang agar jumlah kolom otomatis menyusut menjadi 1 kolom (menumpuk ke bawah) saat layar mengecil tanpa perlu merusak susunan kartu. (Petunjuk: Hubungkan dengan `auto-fit` atau `auto-fill`).

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2:
Kelemahan memaksakan 4 kolom (`repeat(4, 1fr)`) pada layar HP adalah **kartu-kartu tersebut akan terhimpit menjadi sangat sempit secara paksa**. Teks nama golongan darah dan angka jumlah stok akan bertumpuk berantakan, keluar dari batas border kartu (*overflow*), membuat tampilan web menjadi rusak karena lebar HP tidak cukup menampung 4 kolom sekaligus.

### Jawaban 3: Solusi Kolom Otomatis Responsif:
Untuk membuat jumlah kolom menyesuaikan diri secara otomatis berdasarkan lebar ruang yang tersedia, kita harus mengganti baris `grid-template-columns` dengan fungsi **`auto-fit`** dan **`minmax`** berikut:

```css
.grid-stok {
    display: grid;
    /* 
       Membagi kolom dinamis: Lebar minimal kartu 200px. 
       Jika sisa ruang layar tidak cukup memuat 200px, 
       kartu otomatis meluncur turun membuat baris baru di bawahnya.
    */
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 20px;
}
```
Dengan satu baris properti cerdas ini, tampilan grid akan otomatis menjadi 4 kolom di monitor PC, 2 kolom di tablet, dan langsung menumpuk rapi menjadi 1 kolom di layar HP tanpa bantuan media query tambahan!
