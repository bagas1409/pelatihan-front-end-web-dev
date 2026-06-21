# Hari 02: CSS3 Box Model & Layouting Dasar
## Memahami Bagaimana Jarak, Ketebalan Garis, dan Batas Elemen Diatur Pada Kartu Informasi PMI

Selamat datang di Hari 02! Hari ini kita akan mempelajari cara mengatur letak dan jarak elemen menggunakan **CSS3 Box Model** (Model Kotak CSS) serta memahami perbedaan tipe tampilan elemen.

Setiap kali Anda melihat sebuah kartu informasi stok darah atau tombol "Daftar" di layar web, browser sebenarnya menggambar elemen tersebut sebagai sebuah **kotak 4 lapis**. Memahami cara mengontrol keempat lapisan ini adalah kunci utama agar tampilan halaman web Anda rapi, tidak bertabrakan, dan memiliki presisi piksel yang pas.

---

## 1. Konsep CSS Box Model

### 💡 Analogi Dunia Nyata: "Mengirim Lukisan Kaca dalam Kotak Kayu Ekspedisi"
Bayangkan Anda ingin mengirimkan lukisan kaca berharga (Teks/Konten) ke kantor cabang PMI Metro lewat kurir ekspedisi.
*   **Content (Lukisan Kaca)**: Adalah objek utama di tengah (Teks nama golongan darah / angka jumlah stok).
*   **Padding (Busa Pelindung Styrofoam)**: Adalah busa pelindung empuk yang Anda lilitkan di sekeliling lukisan kaca agar tidak terbentur bingkai kayu. (Jarak antara teks dengan garis pembatas kotak terluar).
*   **Border (Kotak Bingkai Kayu)**: Adalah bingkai kayu keras pembatas luar kemasan paket. (Garis pembatas visual elemen).
*   **Margin (Jarak Aman Antar Paket)**: Adalah jarak aman yang Anda berikan agar paket Anda tidak menempel bergesekan dengan paket milik orang lain di dalam truk kurir. (Jarak kosong di luar kotak elemen yang mendorong elemen tetangga menjauh).

---

## 2. Empat Lapisan CSS Box Model Secara Visual

```
┌─────────────────────────────────────────────┐
│                   Margin                    │  <- Dorongan di luar elemen
│   ┌─────────────────────────────────────┐   │
│   │               Border                │   │  <- Garis pembatas visual
│   │   ┌─────────────────────────────┐   │   │
│   │   │           Padding           │   │   │  <- Ruang kosong di dalam elemen
│   │   │   ┌─────────────────────┐   │   │   │
│   │   │   │       Content       │   │   │   │  <- Teks / Gambar Utama
│   │   │   └─────────────────────┘   │   │   │
│   │   └─────────────────────────────┘   │   │
│   └─────────────────────────────────────┘   │
└─────────────────────────────────────────────┘
```

---

## 3. Jenis Tampilan Elemen: Block vs Inline

Sebelum menyusun layout, Anda harus tahu bahwa HTML membagi elemen menjadi dua jenis perilaku dasar:

*   **Display: Block** (Tipe Dominan/Rakus Baris)
    *   Mengambil lebar 100% layar secara otomatis (satu baris penuh).
    *   Bisa diatur tinggi (`height`) dan lebarnya (`width`).
    *   *Contoh*: Tag `<div>`, `<h1>` s.d `<h6>`, `<p>`, `<section>`.
*   **Display: Inline** (Tipe Antre Menyamping)
    *   Lebarnya hanya selebar konten di dalamnya (tidak rakus baris).
    *   Abaikan pengaturan `width` dan `height` (tidak bisa diubah ukurannya).
    *   *Contoh*: Tag `<span>`, `<a>`, `<strong>`, `<em>`.

---

## 4. Studi Kasus PMI: Membuat Kartu Status Stok Darah

Mari kita buat selektor CSS sederhana untuk menggambar kartu stok darah golongan darah A yang kritis menggunakan konsep Box Model yang benar.

### File HTML & CSS Gabungan:

```html
<!DOCTYPE html>
<html lang="id">
<head>
    <style>
        /* Desain Kotak Kartu */
        .kartu-stok {
            /* 1. Atur Ukuran Area Konten */
            width: 250px;
            
            /* 2. Busa Pelindung Dalam (Padding) */
            padding: 20px;
            
            /* 3. Kotak Pembatas (Border) */
            border: 2px solid #ef4444; /* Garis merah penanda kritis */
            border-radius: 12px; /* Membuat sudut kotak melengkung halus */
            
            /* 4. Jarak Kosong Luar (Margin) agar tidak menempel kartu lain */
            margin: 15px;
            
            /* Penunjang Estetika */
            background-color: #ffffff;
            font-family: sans-serif;
        }

        .judul-golongan {
            margin: 0 0 10px 0; /* Matikan margin atas/samping, beri jarak bawah 10px */
            color: #1e293b;
        }

        .jumlah-kantong {
            font-size: 24px;
            font-weight: bold;
            color: #dc2626;
            margin: 0;
        }
    </style>
</head>
<body>

    <!-- Elemen bertipe Display: Block -->
    <div class="kartu-stok">
        <h3 class="judul-golongan">Golongan Darah A</h3>
        <p class="jumlah-kantong">8 Kantong</p>
    </div>

</body>
</html>
```

---

## 5. Latihan Soal Mandiri
1. Amati pengaturan `.kartu-stok` di atas. Jika Anda menambahkan kode `box-sizing: border-box;` ke dalam CSS kartu tersebut, jelaskan apa pengaruhnya terhadap cara browser menghitung total lebar fisik kotak kartu tersebut di layar.
2. Mengapa tag `<span>` tidak akan merespon jika kita memberikan nilai `width: 300px;` di CSS-nya? Sebutkan properti `display` apa yang harus kita pasang agar tag `<span>` tersebut mau diatur ukurannya namun tetap bisa berbaris menyamping.

---

## 6. Kunci Jawaban Soal & Penjelasan

### Jawaban 1:
Secara default, browser menghitung total lebar elemen menggunakan aturan `content-box`. Artinya, total lebar = `width (250px)` + `padding-kiri (20px)` + `padding-kanan (20px)` + `border-kiri (2px)` + `border-kanan (2px)` = **294px**. Hal ini sering merusak layout karena kotak menjadi lebih gemuk dari perkiraan.
Jika kita menambahkan **`box-sizing: border-box;`**, browser dipaksa mengunci total lebar fisik kotak di angka **250px**. Browser akan otomatis menyusutkan area konten dalam agar total kumulatif lebar tetap pas di angka 250px. Ini adalah aturan wajib dalam CSS modern untuk menghindari pecahnya tata letak.

### Jawaban 2:
Tag `<span>` bertipe **`Display: Inline`**. Elemen inline secara standar web memang diabaikan dari pengaturan lebar (`width`) dan tinggi (`height`) karena perilakunya dirancang hanya untuk membungkus teks di dalam baris kalimat.
Agar tag `<span>` bisa diatur ukuran lebarnya tetapi tetap bisa berdiri berdampingan secara horizontal (menyamping), kita harus mengubah tipe display-nya menjadi **`display: inline-block;`** di dalam file CSS.
