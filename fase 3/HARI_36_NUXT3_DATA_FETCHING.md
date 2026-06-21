# Hari 36: Data Fetching di Nuxt 3
## Memahami Perbedaan $fetch, useFetch, & useLazyFetch Serta Cara Memilih Metode yang Tepat

Selamat datang di Hari 36! Hari ini kita memulai **Fase 3**. Di fase ini kita akan memfokuskan pembelajaran pada integrasi penarikan data dari server API luar, pengelolaan state terpusat (Pinia), dan penanganan form. Materi hari ini membahas 3 mekanisme data fetching di Nuxt 3: **`$fetch`**, **`useFetch`**, dan **`useLazyFetch`**.

Meskipun di Fase 1 kita sudah mempelajari Fetch API bawaan browser, Nuxt 3 membungkus sistem fetch tersebut dengan cara yang lebih ramah Server-Side Rendering (SSR) agar browser tidak memanggil API dua kali berturut-turut (di server dan di client) yang bisa merusak performa loading website.

---

## 1. Perbedaan Utama Tiga Mekanisme Fetch

### A. `$fetch` (Pemicu Aksi Berbasis Klik / Event)
*   **Karakteristik**: Berperilaku persis seperti Fetch API bawaan browser. Dia murni fungsi asinkron biasa.
*   **Kapan digunakan**: **Hanya digunakan di dalam fungsi event handler** (seperti fungsi klik tombol submit form `@click="kirimData"`, atau proses login).
*   *Larangan*: Jangan pernah memanggil `$fetch` secara langsung di root tag `<script setup>` tanpa dibungkus fungsi, karena Nuxt akan memanggil API tersebut dua kali (sekali saat merakit HTML di server, dan sekali lagi setelah HTML sampai di browser client).

### B. `useFetch` (Pemuatan Data Awal Halaman - Non-blocking)
*   **Karakteristik**: Composable utama Nuxt yang secara pintar menyinkronkan data server ke client tanpa duplikasi pemicuan request API (*Hydration Friendly*).
*   **Kapan digunakan**: Untuk mengambil data yang **wajib tampil sejak awal halaman pertama kali dimuat** (seperti daftar stok darah di halaman index).
*   *Perilaku*: Default-nya memblokir perpindahan halaman sementara waktu (Loading) sampai data selesai diunduh dari server API.

### C. `useLazyFetch` (Pemuatan Data Latar Belakang - Non-blocking)
*   **Karakteristik**: Sama seperti `useFetch`, namun dia **tidak memblokir perpindahan halaman**. Halaman baru akan langsung terbuka seketika, dan data API dimuat menyusul di latar belakang secara bertahap.
*   **Kapan digunakan**: Untuk halaman dashboard dengan data super besar agar pengguna tidak bosan menunggu layar loading saat berpindah rute menu.

---

## 2. Struktur Sintaks Penggunaan

Berikut adalah cara membedakan penulisan ketiganya:

```html
<script setup>
import { ref } from 'vue';

// 1. useFetch: Memuat daftar stok sejak awal (Blocking Route transition)
// Data disimpan di variabel 'stocks' hasil destructuring
const { data: stocks, error } = await useFetch('https://api.pmi.com/stocks');

// 2. useLazyFetch: Memuat data grafik di latar belakang (Non-blocking)
// pending: bernilai true jika data sedang diunduh di latar belakang
const { data: grafikData, pending } = useLazyFetch('https://api.pmi.com/chart-data');

// 3. $fetch: Digunakan di dalam fungsi aksi/event
const inputNama = ref('');
const prosesDaftar = async () => {
  try {
    const hasil = await $fetch('https://api.pmi.com/relawan', {
      method: 'POST',
      body: { nama: inputNama.value }
    });
    alert('Sukses Mendaftar!');
  } catch (err) {
    console.error(err);
  }
};
</script>
```

---

## 3. Studi Kasus PMI: Halaman Stok Darah dengan useFetch

Mari kita buat halaman dinamis pertama yang memanggil data stok darah dari API tiruan secara aman.

```html
<!-- pages/stok-darah.vue -->
<template>
  <div class="max-w-md mx-auto p-6 bg-white rounded-3xl border shadow-sm mt-8 space-y-4">
    <h3 class="font-bold text-slate-800 border-b pb-3">🩸 Live Stok Darah UDD</h3>

    <!-- Menampilkan pesan error jika server API padam -->
    <div v-if="error" class="p-4 bg-red-50 text-red-800 rounded-xl text-xs">
      🚨 Gagal memuat data dari server: {{ error.message }}
    </div>

    <!-- Render List Data dari API -->
    <ul v-else-if="daftarStok" class="space-y-2 text-sm">
      <li v-for="item in daftarStok" :key="item.golongan" class="flex justify-between py-2 border-b">
        <span>Golongan {{ item.golongan }}</span>
        <strong :class="item.jumlah < 10 ? 'text-red-500' : 'text-slate-700'">
          {{ item.jumlah }} Kantong
        </strong>
      </li>
    </ul>

    <!-- Tombol Refresh Data Manual -->
    <button 
      @click="refetchData" 
      class="w-full py-2 bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold rounded-xl text-xs transition-colors"
    >
      🔄 Segarkan Data
    </button>
  </div>
</template>

<script setup>
// Mengambil data dengan useFetch
// refresh: fungsi helper bawaan untuk memicu pemicuan API ulang secara manual
const { data: daftarStok, error, refresh } = await useFetch('https://pmi-backend-api.onrender.com/api/stocks');

const refetchData = () => {
  refresh(); // Picu pembaruan data dari database API terbaru
};
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati kode tombol refresh di atas: `@click="refresh"`.
2. Jelaskan apa akibatnya jika kita mengganti pemanggilan `useFetch` di root script setup dengan fungsi `$fetch` biasa tanpa dibungkus event handler (seperti: `const daftarStok = await $fetch('...')`).
3. Sebutkan parameter apa saja yang dikembalikan (destructured) oleh composable `useFetch` selain `data` dan `error`.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Bahaya $fetch di Root Script
*   **Akibat**: Terjadi pemanggilan API ganda (*Double Request*) dan pemborosan bandwidth server.
*   **Alasan**: Nuxt 3 adalah framework SSR. Saat render di server Node.js, `$fetch` akan dieksekusi sekali untuk mengambil data dan merakit HTML. Setelah HTML dikirim ke browser, browser akan memuat ulang file JS. Karena `$fetch` ditulis langsung di root script, browser terpaksa **menembak ulang API tersebut untuk kedua kalinya** karena `$fetch` tidak memiliki sistem penyimpanan cache data temporer. Composable `useFetch` menyelesaikan ini dengan memuat data di server, lalu mengemas datanya ke dalam payload HTML (*state hydration*) sehingga browser client tidak perlu memanggil API lagi saat pertama kali dimuat.

### Jawaban 3: Return Parameters useFetch
*   **`pending`**: Variabel boolean reaktif yang bernilai `true` selama proses request HTTP sedang berjalan di internet, dan menjadi `false` setelah respon diterima.
*   **`refresh` / `execute`**: Fungsi callback untuk memaksa browser menembak ulang API tersebut guna mendapatkan data terbaru (sangat berguna untuk tombol reload tabel).
*   **`status`**: String status koneksi, bernilai `'idle'`, `'pending'`, `'success'`, atau `'error'`.
