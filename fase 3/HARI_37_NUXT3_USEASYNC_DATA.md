# Hari 37: Custom Fetching dengan useAsyncData
## Melakukan Kueri Gabungan Banyak Endpoint API Medis Secara Efisien

Selamat datang di Hari 37! Hari ini kita akan mempelajari fungsi pencari data tingkat lanjut: **`useAsyncData`** di Nuxt 3.

Jika kemarin kita menggunakan `useFetch` untuk menembak satu URL API tunggal secara langsung, ada kalanya di dashboard PMI Lampung kita harus menembak **beberapa endpoint API sekaligus secara bersamaan** (seperti memanggil data profil admin, total kantong darah, dan log aktivitas relawan dalam 1 siklus render) atau membungkus library HTTP pihak ketiga (seperti Axios). `useAsyncData` bertugas membungkus seluruh operasi asinkron majemuk tersebut menjadi satu kesatuan yang rapi.

---

## 1. Kegunaan dan Filosofi useAsyncData

`useAsyncData` bertindak sebagai payung pelindung asinkron. Dia menerima sebuah kunci unik (Key) sebagai pengenal cache di parameter pertama, dan sebuah fungsi asinkron (callback) di parameter kedua.

### 💡 Analogi Dunia Nyata: "Nampan Pembawa Pesanan Makanan Dokter UDD"
Bayangkan Anda adalah asisten dokter di UDD PMI Lampung. Dokter UDD meminta 3 barang dari gudang logistik yang letaknya berjauhan: 1 botol alkohol antiseptik, 1 pak sarung tangan karet, dan 1 lembar berkas log formulir donor.
*   **Tanpa useAsyncData (Bolak-balik 3 kali)**: Anda berjalan mengambil alkohol, menyerahkannya ke dokter. Berjalan lagi mengambil sarung tangan, menyerahkannya. Dokter terpaksa menunggu 3 kali jeda pengiriman yang melelahkan.
*   **Dengan useAsyncData (Satu Nampan Besar)**: Anda mengambil sebuah nampan kayu besar. Anda berkeliling gudang, menaruh ketiga barang tersebut di atas nampan, lalu membawanya sekaligus dalam satu kali perjalanan menghampiri meja dokter. Dokter menerima semua kebutuhan dalam satu momen yang sama.

---

## 2. Struktur Sintaks useAsyncData

```javascript
// 'rekap-pmi' adalah kunci unik (cache key) agar data tidak di-fetch ulang jika sudah ada
const { data: rekap, pending, error } = await useAsyncData('rekap-pmi', async () => {
  // Kita bebas memanggil banyak API asinkron sekaligus di dalam blok ini!
  const [resStok, resRelawan] = await Promise.all([
    $fetch('https://api.pmi.com/stocks'),
    $fetch('https://api.pmi.com/volunteers')
  ]);

  // Kembalikan objek gabungan
  return {
    daftarStok: resStok,
    totalRelawan: resRelawan.length
  };
});
```

---

## 3. Studi Kasus PMI: Gabungan Dashboard Info Medis UDD

Mari kita buat halaman beranda dashboard yang memuat data stok darah kritis dan data agenda donor keliling secara paralel menggunakan `useAsyncData`.

```html
<!-- pages/dashboard.vue -->
<template>
  <div class="max-w-xl mx-auto p-6 bg-slate-50 min-h-screen space-y-6">
    <h2 class="font-bold text-slate-800 text-lg">📊 Monitor Pusat UDD Lampung</h2>

    <!-- Loading State -->
    <div v-if="pending" class="text-xs text-slate-400 animate-pulse">
      ⏳ Mengumpulkan berkas logistik dari berbagai server cabang UDD...
    </div>

    <!-- Menampilkan Error -->
    <div v-else-if="error" class="p-4 bg-red-50 text-red-800 rounded-2xl text-xs">
      🚨 Gagal memproses data: {{ error.message }}
    </div>

    <!-- Tampilan Data Gabungan -->
    <div v-else class="grid grid-cols-1 md:grid-cols-2 gap-6">
      
      <!-- Box 1: Rekap Stok -->
      <div class="bg-white p-6 rounded-2xl border shadow-sm space-y-2">
        <h4 class="text-xs font-bold text-slate-400 uppercase">Stok Darah Terdaftar</h4>
        <p class="text-2xl font-extrabold text-slate-800">
          {{ dataGabungan.daftarStok.length }} Golongan
        </p>
      </div>

      <!-- Box 2: Total Agenda Keliling -->
      <div class="bg-white p-6 rounded-2xl border shadow-sm space-y-2">
        <h4 class="text-xs font-bold text-slate-400 uppercase">Agenda Donor Aktif</h4>
        <p class="text-2xl font-extrabold text-red-500">
          {{ dataGabungan.jumlahAgenda }} Kegiatan
        </p>
      </div>

    </div>
  </div>
</template>

<script setup>
// Gunakan useAsyncData untuk query paralel majemuk
const { data: dataGabungan, pending, error } = await useAsyncData('dashboard-medis', async () => {
  const BASE_URL = 'https://pmi-backend-api.onrender.com/api';
  
  // Jalankan fetch API secara paralel menggunakan Promise.all demi menghemat waktu loading
  const [stocks, donations] = await Promise.all([
    $fetch(`${BASE_URL}/stocks`),
    $fetch(`${BASE_URL}/donations`)
  ]);

  // Susun data nampan hasil gabungan
  return {
    daftarStok: stocks,
    jumlahAgenda: donations.length
  };
});
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati parameter pertama `'dashboard-medis'` pada fungsi `useAsyncData` di atas.
2. Jelaskan fungsi dari pemberian string kunci unik tersebut (Unique Key) bagi sistem caching internal Nuxt 3 dan apa akibatnya jika kita menggunakan kunci yang sama pada dua fungsi `useAsyncData` yang berbeda isi API-nya.
3. Sebutkan kelebihan pemanggilan API paralel menggunakan `Promise.all` dibandingkan menggunakan penulisan asinkron berurutan baris demi baris (sequential await).

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Caching Key useAsyncData
*   **Fungsi Kunci Unik (Key)**: Nuxt menggunakan kunci tersebut sebagai indeks peta penyimpanan data sementara di RAM (*State Cache*). Jika pengguna berpindah halaman lalu kembali lagi, Nuxt mendeteksi kunci `'dashboard-medis'` sudah memiliki data lama tersimpan. Nuxt akan langsung menyajikan data cache tersebut dalam 1 milidetik tanpa perlu membuang kuota menembak API server lagi.
*   **Bahaya Kunci Duplikat**: Jika dua fungsi berbeda menggunakan kunci yang sama, fungsi kedua akan secara tidak sengaja **menimpa atau menggunakan data cache milik fungsi pertama**. Hal ini memicu bug visual yang aneh di mana halaman agenda donor keliling secara keliru malah menampilkan tabel golongan darah karena bertukar data cache.

### Jawaban 3: Keunggulan Promise.all
*   **Sequential (Berurutan - Lambat)**:
    ```javascript
    const dataA = await fetchA; // Butuh 2 detik
    const dataB = await fetchB; // Baru jalan setelah A selesai. Butuh 2 detik. Total = 4 detik!
    ```
*   **Parallel (Bersamaan - Cepat)**:
    `Promise.all` menembak kedua API tersebut ke internet secara bersamaan (paralel). Browser hanya membutuhkan total waktu tunggu maksimal setara dengan API yang paling lambat (yaitu **2 detik**), memotong durasi tunggu loading halaman hingga 50%!
