# Hari 81: Membuat Grafik Lingkaran (Pie Chart)
## Menyajikan Persentase Distribusi Golongan Darah Menggunakan Doughnut & Pie Chart.js

Selamat datang di Hari 81! Hari ini kita akan mempelajari cara merakit **Grafik Lingkaran** (Pie / Doughnut Chart) menggunakan Chart.js di Nuxt 3.

Diagram lingkaran sangat ideal untuk menunjukkan perbandingan proporsi kontribusi persentase terhadap satu angka utuh (total 100%). Kita akan memetakan persentase sebaran golongan darah para relawan aktif di UDD PMI Lampung agar terlihat golongan darah mana yang paling mendominasi porsi database.

---

## 1. Mengenal Modul Doughnut & ArcElement

Untuk menggambar diagram lingkaran di Chart.js, kita membutuhkan modul utama:
*   **`ArcElement`**: Modul yang bertugas melengkungkan garis canvas membentuk sayatan busur lingkaran (*Pie Slices*).
*   **`Doughnut`**: Variasi diagram lingkaran yang memiliki lubang donat kosong di bagian tengahnya. Desain doughnut dinilai lebih premium dan modern dibandingkan lingkaran penuh karena kita bisa menaruh angka total di lubang tengahnya.

---

## 2. Studi Kasus PMI: Diagram Persentase Relawan

Mari kita buat halaman diagram donat di berkas **`pages/grafik/relawan-pie.vue`**.

```html
<!-- pages/grafik/relawan-pie.vue -->
<template>
  <div class="space-y-6">
    <div>
      <h2 class="text-xl font-bold text-slate-800">🍩 Distribusi Golongan Darah Relawan</h2>
      <p class="text-xs text-slate-500 mt-1">Persentase proporsi golongan darah relawan aktif terdaftar</p>
    </div>

    <!-- Kontainer Doughnut (Membatasi Tinggi Lebar Agar Proporsional) -->
    <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm max-w-sm">
      <ClientOnly>
        <Doughnut 
          :data="chartData" 
          :options="chartOptions" 
        />
        
        <template #fallback>
          <div class="h-64 bg-slate-100 rounded-2xl flex items-center justify-center text-slate-400 animate-pulse">
            ⏳ Mempersiapkan diagram lingkaran...
          </div>
        </template>
      </ClientOnly>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue';
import { Doughnut } from 'vue-chartjs';
import { 
  Chart as ChartJS, 
  Tooltip, 
  Legend, 
  ArcElement 
} from 'chart.js';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

// Daftarkan modul busur ArcElement untuk Pie/Doughnut
ChartJS.register(Tooltip, Legend, ArcElement);

// DATA PERSENTASE (Total Relawan: 200 Orang)
const chartData = ref({
  labels: ['Golongan A', 'Golongan B', 'Golongan AB', 'Golongan O'],
  datasets: [
    {
      label: 'Jumlah Anggota Relawan',
      // Memberikan deretan warna berbeda untuk tiap irisan sayatan
      backgroundColor: [
        '#f87171', // Red 400 (A)
        '#60a5fa', // Blue 400 (B)
        '#fbbf24', // Amber 400 (AB)
        '#34d399'  // Emerald 400 (O)
      ],
      hoverOffset: 12, // Sayatan membesar keluar saat kursor melayang di atasnya
      borderWidth: 4,  // Ketebalan garis pembatas putih antar sayatan
      borderColor: '#ffffff', // Warna pembatas putih (memberi jarak spasi visual)
      data: [50, 40, 20, 90] // Proporsi jumlah anggota
    }
  ]
});

const chartOptions = ref({
  responsive: true,
  maintainAspectRatio: true,
  plugins: {
    legend: {
      position: 'bottom',
      labels: {
        boxWidth: 12, // Memperkecil kotak indikator warna legenda
        padding: 20
      }
    }
  },
  // Opsi khusus Doughnut untuk mengatur ukuran lubang donat tengah (dalam persen)
  cutout: '70%' // Semakin besar angkanya, cincin donat semakin tipis elegan
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati konfigurasi `cutout: '70%'` pada file di atas.
2. Jelaskan fungsi dari parameter `cutout` tersebut pada tipe diagram Doughnut dan apa jadinya jika kita merubah nilainya menjadi `'0%'` (atau `0`).
3. Sebutkan kelebihan visual dari penggunaan opsi `hoverOffset: 12` saat petugas mengarahkan kursor mouse ke salah satu potongan warna golongan darah.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Mengenal Properti Cutout Doughnut
*   **Fungsi `cutout`**: Mengatur radius diameter lubang putih di bagian tengah diagram donat (dalam satuan persentase terhadap diameter luar lingkaran). Nilai `'70%'` memotong 70% area tengah menyisakan 30% cincin luar yang tipis modis.
*   **Akibat diubah ke 0**: Jika disetel ke `0` atau `'0%'`, lubang tengah akan ditutup rapat secara total. Diagram donat akan **berubah wujud kembali menjadi diagram lingkaran utuh (Pie Chart standar)** tanpa ada lubang di tengahnya.

### Jawaban 3: Kegunaan Efek Hover Offset (Micro-interaction)
*   **Umpan Balik Visual Interaktif**: Saat kursor menyentuh sayatan golongan darah O, kepingan hijau tersebut akan **meluncur keluar sejauh 12 pixel** memisahkan diri dari lingkaran utama. 
*   Interaksi mikro ini memberikan penegasan visual yang menyenangkan, memperjelas area data mana yang sedang aktif dianalisis oleh petugas UDD tanpa terganggu kepingan warna di sekitarnya.
