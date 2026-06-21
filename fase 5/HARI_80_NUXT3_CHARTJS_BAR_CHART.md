# Hari 80: Membuat Grafik Batang (Bar Chart) Chart.js
## Menyusun Diagram Batang Stok Kantong Darah Menggunakan Chart.js & vue-chartjs

Selamat datang di Hari 80! Hari ini kita akan mempraktikkan pembuatan grafik dinamis pertama kita: **Grafik Batang (Bar Chart)** menggunakan pustaka **Chart.js** dan **`vue-chartjs`** di Nuxt 3.

Kita akan menggambar diagram batang yang membandingkan persediaan kantong darah golongan A, B, AB, dan O di kulkas logistik UDD PMI Lampung. Grafik ini akan memperlihatkan golongan darah mana yang persediaannya melimpah dan mana yang kritis di bawah batas garis aman.

---

## 1. Langkah Instalasi Library Grafik

Jalankan perintah penginstalan berikut di terminal root proyek Nuxt Anda:
```bash
npm install chart.js vue-chartjs
```

---

## 2. Studi Kasus PMI: Grafik Stok Darah UDD

Mari kita buat halaman laporan grafik di berkas **`pages/grafik/stok.vue`**.

```html
<!-- pages/grafik/stok.vue -->
<template>
  <div class="space-y-6">
    <div>
      <h2 class="text-xl font-bold text-slate-800">📊 Statistik Persediaan Darah</h2>
      <p class="text-xs text-slate-500 mt-1">Perbandingan jumlah kantong darah aktif di UDD Lampung</p>
    </div>

    <!-- Kontainer Chart Box (Membatasi Lebar Agar Tidak Terlalu Besar) -->
    <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm max-w-lg">
      <ClientOnly>
        <!-- Komponen Bar Chart bawaan vue-chartjs -->
        <Bar 
          :data="chartData" 
          :options="chartOptions" 
        />
        
        <template #fallback>
          <div class="h-64 bg-slate-100 rounded-2xl flex items-center justify-center text-xs text-slate-400 animate-pulse">
            ⏳ Mempersiapkan diagram batang...
          </div>
        </template>
      </ClientOnly>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue';
import { Bar } from 'vue-chartjs';
// Wajib mengregistrasikan modul-modul Chart.js yang digunakan agar file bundle tetap ringan
import { 
  Chart as ChartJS, 
  Title, 
  Tooltip, 
  Legend, 
  BarElement, 
  CategoryScale, 
  LinearScale 
} from 'chart.js';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

// Daftarkan modul ke engine Chart.js
ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale);

// 1. DATA UNTUK GRAFIK (Label & Nilai Baris)
const chartData = ref({
  labels: ['Golongan A', 'Golongan B', 'Golongan AB', 'Golongan O'], // Sumbu X
  datasets: [
    {
      label: 'Jumlah Kantong Darah (Bag)',
      backgroundColor: '#f87171', // Warna merah pastel Tailwind untuk batang
      borderRadius: 12, // Membuat ujung batang membulat premium
      data: [45, 98, 12, 120] // Sumbu Y (Nilai Stok Riil)
    }
  ]
});

// 2. OPSI KONFIGURASI GRAFIK
const chartOptions = ref({
  responsive: true, // Grafik otomatis mengecil jika dibuka di handphone
  maintainAspectRatio: true,
  plugins: {
    legend: {
      position: 'bottom', // Posisikan label keterangan di bawah grafik
      labels: {
        font: {
          family: 'sans-serif',
          weight: 'bold'
        }
      }
    }
  },
  scales: {
    y: {
      beginAtZero: true, // Sumbu Y wajib dimulai dari angka 0
      grid: {
        color: '#f1f5f9' // Warna garis bantu horizontal abu-abu tipis
      }
    },
    x: {
      grid: {
        display: false // Matikan garis bantu vertikal agar grafik bersih
      }
    }
  }
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati kode registrasi modul: `ChartJS.register(...)` di atas.
2. Jelaskan fungsi dari pemanggilan metode `ChartJS.register()` tersebut dan mengapa kita harus memilah modul skala secara manual (seperti `CategoryScale` dan `LinearScale`) daripada mengimpor seluruh pustaka Chart.js secara utuh.
3. Sebutkan kegunaan dari opsi `borderRadius: 12` pada properti datasets di atas bagi aspek estetika desain antarmuka.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Konsep Tree Shaking pada Chart.js
*   **Fungsi Registrasi Manual**: Chart.js versi terbaru menerapkan arsitektur modular. Kita hanya mendaftarkan modul-modul visual yang benar-benar kita render di halaman tersebut saja (seperti `BarElement` untuk diagram batang).
*   **Keuntungan (Tree Shaking)**: Jika kita mengimpor seluruh pustaka Chart.js secara utuh (termasuk modul radar chart, bubble chart, scatter chart yang tidak kita pakai), ukuran file JavaScript website kita akan membengkak sangat besar. Registrasi modular memangkas ukuran file bundle akhir (*Bundle Size*) saat dibuild, mempercepat durasi loading awal website secara drastis.

### Jawaban 3: Estetika Desain Modern (Rounded Bars)
*   **Visual Premium**: Secara default, Chart.js menggambar diagram batang dengan sudut kotak tajam 90 derajat yang terkesan kaku dan kuno ala aplikasi perkantoran jadul. 
*   Pemberian properti `borderRadius: 12` melunakkan ujung-ujung batang tersebut menjadi membulat halus (*Rounded Corners*), menyelaraskan desain grafik dengan tema antarmuka dashboard DonorKu yang berdesain lengkung premium.
```
[FASE 5: VISUALISASI DIAGRAM BATANG STOK DARAH SUKSES]
```
