# Hari 79: Pengenalan Visualisasi Data Laporan PMI
## Memahami Filosofi Grafik Statistik Membantu Kepala UDD Uji Cepat Kelayakan Logistik

Selamat datang di Hari 79! Hari ini kita akan mempelajari konsep **Visualisasi Data** (Data Visualization) sebelum kita mengode pustaka grafik di Hari 80.

Setiap akhir bulan, kepala cabang UDD PMI Lampung harus menganalisis tren donor darah (seperti: *"Golongan darah apa yang paling sering menipis?"*, atau *"Di bulan apa biasanya terjadi lonjakan pendaftaran relawan baru?"*). Membaca database mentah berbentuk deretan angka ribuan baris di tabel Excel akan membuat kepala UDD pusing dan lambat mengambil kesimpulan. Kita akan belajar memindahkan angka angka tersebut menjadi diagram visual yang bisa dipahami dalam hitungan detik.

---

## 1. Tiga Tipe Grafik Utama untuk Dashboard Medis

Untuk melahirkan laporan eksekutif yang bernilai premium, kita harus memilih tipe grafik yang tepat sesuai karakteristik datanya:

| Tipe Grafik | Kegunaan Terbaik | Contoh Kasus PMI |
| :--- | :--- | :--- |
| **Diagram Batang (Bar Chart)** | Membandingkan nilai antar kategori yang berbeda. | Menampilkan jumlah stok kantong darah per golongan (A, B, AB, O). |
| **Diagram Garis (Line Chart)** | Memantau pergerakan tren naik-turun dari waktu ke waktu. | Grafik grafik jumlah pendaftar relawan donor baru selama 12 bulan terakhir. |
| **Diagram Lingkaran (Pie/Doughnut)** | Memantau porsi persentase pembagian bagian dari satu total. | Persentase sebaran golongan darah pasien penerima donor di Lampung. |

---

## 2. Mengenal Chart.js dan Chartify di Vue 3

Di ekosistem Vue, kita menggunakan **Chart.js** (engine render grafik berbasis Canvas HTML5 terpopuler) dikawinkan dengan pembungkus **`vue-chartjs`** agar penulisan grafik bisa dikendalikan secara reaktif menggunakan variabel state Vue.

Di Hari 80, kita akan menginstal modul-modul ini dan mulai menggambar grafik batang persediaan kantong darah UDD PMI secara nyata.

---

## 3. Latihan Soal Mandiri
1. Amati pembagian jenis grafik pada tabel di atas.
2. Jika kepala UDD PMI ingin memantau **penurunan drastis jumlah kantong darah yang berhasil dikumpulkan UDD selama masa libur mudik lebaran 7 hari berturut-turut**, sebutkan tipe grafik mana yang paling tepat dipilih untuk menyajikan laporan tersebut dan jelaskan alasannya.
3. Jelaskan akibat buruknya bagi kenyamanan membaca laporan jika kita memaksakan menggunakan **Pie Chart** untuk menampilkan perbandingan data stok darah dari **150 Rumah Sakit Mitra** secara bersamaan.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pemilihan Grafik Tren Garis (Line Chart)
*   **Pilihan Terbaik**: **Diagram Garis (Line Chart)**.
*   **Alasan**: Kasus mudik lebaran mengamati pergerakan data berdasarkan urutan kronologis waktu (hari ke-1 s.d hari ke-7). Diagram garis sangat unggul dalam memperlihatkan kelandaian sudut garis (apakah garis menukik tajam ke bawah pada hari H lebaran), memudahkan identifikasi tren kepanikan stok secara dramatis dalam sekali tatapan mata.

### Jawaban 3: Kesalahan Penggunaan Pie Chart untuk Data Besar
*   **Visual Tersumbat (Visual Clutter)**: Diagram lingkaran (*Pie Chart*) dirancang untuk membagi lingkaran menjadi juring-juring persentase (ideal maksimal 5 s.d 7 kategori). 
*   Jika kita memasukkan 150 rumah sakit mitra ke dalam satu lingkaran pie, lingkaran akan terpecah menjadi 150 sayatan garis yang sangat tipis bagai jarum berhimpitan, membuat teks legenda warna bertumpuk-tumpuk rusak tidak terbaca, dan merusak kegunaan informasi data tersebut secara keseluruhan. Untuk kasus perbandingan 150 RS, diagram batang horizontal (*Horizontal Bar Chart*) dengan fitur scroll adalah pilihan terbaik.
