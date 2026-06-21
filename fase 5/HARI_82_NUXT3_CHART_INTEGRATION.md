# Hari 82: Sinkronisasi Grafik dengan API Backend
## Memetakan Array Data JSON Hasil Fetch API useApi Ke Struktur Sumbu X & Sumbu Y Grafik Chart.js

Selamat datang di Hari 82! Hari ini kita akan mempelajari teknik menghubungkan **Grafik Chart.js dengan Server API Backend Express.js** yang sesungguhnya.

Menyajikan data grafik statis (seperti di Hari 80 & 81) tentu tidak ada gunanya untuk kebutuhan dinamis di lapangan. Kita harus menarik data transaksi logistik riil dari server (`GET /api/stocks`), mengekstrak datanya menggunakan bantuan Javascript **`Array.map()`**, lalu menyuplai array hasil ekstraksi tersebut ke dalam properti `labels` (sumbu X) dan `data` (sumbu Y) pada grafik secara reaktif.

---

## 1. Alur Pemetaan Data untuk Grafik

Database backend Express mengembalikan array objek logistik berbentuk JSON:
```json
[
  { "id": 1, "golongan": "A", "jumlah": 45 },
  { "id": 2, "golongan": "B", "jumlah": 98 }
]
```
Untuk disuplai ke Chart.js, kita harus memecahnya menjadi dua array terpisah:
1.  **Array Label (Sumbu X)**: `['Golongan A', 'Golongan B']` $\rightarrow$ didapat dari `array.map(item => 'Golongan ' + item.golongan)`
2.  **Array Angka (Sumbu Y)**: `[45, 98]` $\rightarrow$ didapat dari `array.map(item => item.jumlah)`

---

## 2. Studi Kasus PMI: Grafik Stok Darah Terintegrasi Real-time

Mari kita buat halaman dashboard grafik dinamis di `pages/grafik/stok-api.vue` yang terhubung langsung ke API server backend menggunakan helper `useApi`.

```html
<!-- pages/grafik/stok-api.vue -->
<template>
  <div class="space-y-6">
    <div class="flex justify-between items-center border-b pb-4">
      <div>
        <h2 class="text-xl font-bold text-slate-800">📊 Live Chart UDD</h2>
        <p class="text-xs text-slate-500 mt-1">Data grafik terhubung langsung dengan database server backend</p>
      </div>
      <button @click="refresh" class="bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-bold px-4 py-2 rounded-xl">
        🔄 Sinkronkan Grafik
      </button>
    </div>

    <!-- Area Peringatan Error -->
    <div v-if="error" class="p-4 bg-red-50 text-red-800 text-xs rounded-2xl">
      🚨 Gagal memuat data grafik: {{ error.message }}
    </div>

    <!-- Render Grafik Bar -->
    <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm max-w-lg">
      <!-- Kita menaruh v-if="!pending" agar chart digambar HANYA setelah data API mendarat -->
      <ClientOnly>
        <Bar 
          v-if="!pending && dataStok"
          :data="chartDataComputed" 
          :options="chartOptions" 
        />
        
        <!-- Skeleton Loader saat fetching -->
        <div v-else class="h-64 bg-slate-100 rounded-2xl flex items-center justify-center text-slate-400 animate-pulse text-xs">
          ⏳ Mengambil koordinat stok terbaru...
        </div>
      </ClientOnly>
    </div>

  </div>
</template>

<script setup>
import { computed } from 'vue';
import { Bar } from 'vue-chartjs';
import { 
  Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale 
} from 'chart.js';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale);

// 1. FETCH DATA DARI API BACKEND
const { data: dataStok, pending, error, refresh } = await useApi('/stocks');

// 2. COMPUTED: EKSTRAK DATA API KE CHART.JS FORMAT
const chartDataComputed = computed(() => {
  if (!dataStok.value) return { labels: [], datasets: [] };

  // A. Ekstrak sumbu X (Labels)
  const listLabels = dataStok.value.map(item => `Golongan ${item.golongan}`);

  // B. Ekstrak sumbu Y (Data Angka)
  const listAngka = dataStok.value.map(item => item.jumlah);

  return {
    labels: listLabels,
    datasets: [
      {
        label: 'Kantong Terkumpul (Bag)',
        backgroundColor: '#f87171',
        borderRadius: 12,
        data: listAngka
      }
    ]
  };
});

const chartOptions = {
  responsive: true,
  scales: {
    y: { beginAtZero: true }
  }
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati kode computed property: `chartDataComputed` di atas.
2. Jelaskan mengapa kita disarankan membungkus pemrosesan mapping array API tersebut di dalam **`computed()`** Vue daripada menyalin nilainya ke variabel `ref` biasa secara mentah-mentah di blok `then` fetch.
3. Sebutkan apa akibat buruknya bagi visual grafik jika database API backend Express Anda sedang kosong/mati, sedangkan kita lupa menuliskan validasi pengaman `if (!dataStok.value)` di baris awal computed.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Keunggulan Computed untuk Integrasi API
*   **Auto-Update Dinamis (Reaktivitas)**: Computed property memelihara jalur ketergantungan (*Dependency Tracking*). Kapanpun variabel `dataStok` berubah nilainya (misalnya setelah petugas menekan tombol "Sinkronkan Grafik" yang memicu penarikan data baru dari server), computed akan mendeteksi perubahan tersebut secara instan.
*   Computed akan menghitung ulang map label dan angka terbaru, lalu langsung mengalirkan data baru tersebut ke komponen `<Bar />` untuk menggambar ulang grafik secara otomatis tanpa perlu intervensi coding manual tambahan.

### Jawaban 3: Penanganan Data Kosong (Null Handling)
*   **Mencegah Crash Runtime**: Jika server API mati atau database kosong, variabel `dataStok.value` akan bernilai `null` atau `undefined`.
*   Jika kita langsung mengeksekusi `dataStok.value.map(...)` tanpa validasi `if (!dataStok.value)`, JavaScript akan langsung melempar error merah fatal **`TypeError: Cannot read properties of null (reading 'map')`**. Error runtime ini akan langsung membekukan jalannya seluruh kode website, menyebabkan layar monitor petugas admin memutih hangus (*White Screen of Death*). Validasi awal menjamin grafik aman mengembalikan data kosong `{ labels: [], datasets: [] }` tanpa merusak jalannya website.
