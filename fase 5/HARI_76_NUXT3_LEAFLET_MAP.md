# Hari 76: Integrasi Peta Leaflet.js di Nuxt 3
## Menggambar Kontainer Peta Interaktif Sebaran Kantor UDD PMI & Rumah Sakit Lampung

Selamat datang di Hari 76! Hari ini kita memulai **Fase 5 (Fase Terakhir)**. Di fase ini kita akan fokus pada visualisasi peta spasial, pembuatan grafik statistik, ekspor laporan cetak PDF/Excel, optimasi caching, dan rilis deployment production ke internet.

Materi hari ini akan membahas cara menanamkan **Peta Interaktif** menggunakan library gratis standar industri: **Leaflet.js** di aplikasi Nuxt 3. Kita akan menggambar peta Lampung yang bisa digeser, diperbesar, dan diperkecil oleh pengguna.

---

## 1. Tantangan SSR pada Peta Leaflet

Peta Leaflet.js memiliki karakteristik yang wajib dipahami oleh developer Nuxt:
*   **Akses DOM Langsung**: Leaflet dirancang untuk langsung membaca dan menggambar element HTML `<div>` fisik di browser.
*   **Masalah SSR**: Jika server Node.js Nuxt mencoba merender kode Leaflet saat memproses HTML di belakang layar, server akan langsung melempar error crash fatal karena server tidak memiliki objek browser `window` maupun `document`.
*   **Solusi `<ClientOnly>`**: Kita wajib membungkus kontainer peta di dalam tag bawaan Nuxt **`<ClientOnly>`** agar peta hanya dieksekusi setelah halaman mendarat aman di browser client.

---

## 2. Langkah Penginstalan Leaflet

Jalankan perintah penginstalan berikut di terminal root proyek Anda:
```bash
npm install leaflet
```

---

## 3. Studi Kasus PMI: Menggambar Peta Dasar Provinsi Lampung

Mari kita rancang halaman peta dasar di berkas **`pages/peta/index.vue`**.

```html
<!-- pages/peta/index.vue -->
<template>
  <div class="space-y-6">
    <div>
      <h2 class="text-xl font-bold text-slate-800">🗺️ Peta Monitor Sebaran UDD</h2>
      <p class="text-xs text-slate-500 mt-1">Gunakan peta interaktif di bawah untuk memantau titik koordinat logistik</p>
    </div>

    <!-- 
      ClientOnly -> Menjamin peta Leaflet hanya dimuat di browser client
    -->
    <ClientOnly>
      <div class="bg-white p-4 rounded-3xl border border-slate-200 shadow-sm overflow-hidden">
        <!-- Kontainer Map Wajib Memiliki Tinggi CSS yang Jelas (h-[400px]) -->
        <div id="map-kontainer" class="h-[400px] w-full rounded-2xl z-10"></div>
      </div>

      <!-- Spinner Loader saat server sedang mempersiapkan visual client -->
      <template #fallback>
        <div class="h-[400px] w-full bg-slate-100 rounded-3xl flex items-center justify-center text-xs text-slate-400 animate-pulse">
          ⏳ Mempersiapkan visualisasi peta Lampung...
        </div>
      </template>
    </ClientOnly>

  </div>
</template>

<script setup>
import { onMounted, onUnmounted } from 'vue';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

let mapInstance = null;

// Mengimpor file CSS bawaan Leaflet agar garis batas dan ikon tergambar rapi
// Wajib di-import di script setup agar dimuat oleh webpack/vite
import 'leaflet/dist/leaflet.css';

onMounted(async () => {
  // 1. Impor dinamis library Leaflet hanya saat di client browser
  const L = await import('leaflet');

  // 2. Inisialisasi peta dasar, arahkan titik tengah (Center) ke koordinat kota Bandar Lampung
  // Koordinat Bandar Lampung: Lat -5.397, Lng 105.266 dengan tingkat zoom awal 12
  mapInstance = L.map('map-kontainer').setView([-5.397, 105.266], 12);

  // 3. Muat gambar peta (Tile Layer) gratis dari server OpenStreetMap
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; OpenStreetMap contributors'
  }).addTo(mapInstance);
});

onUnmounted(() => {
  // Bersihkan memori peta saat petugas keluar meninggalkan halaman
  if (mapInstance) {
    mapInstance.remove();
  }
});
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati penggunaan tag `<ClientOnly>` dan template `#fallback` pada file di atas.
2. Jelaskan fungsi dari tag `<ClientOnly>` dan apa yang ditampilkan di layar monitor pengguna jika koneksi browser client sedang berjalan lambat memuat file JavaScript Leaflet.
3. Sebutkan mengapa kontainer peta Leaflet (`<div id="map-kontainer">`) wajib memiliki penyetelan ukuran tinggi CSS yang jelas (seperti kelas `h-[400px]` atau `h-96`) di kodenya.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan ClientOnly & Fallback Loader
*   **Fungsi `<ClientOnly>`**: Menghentikan proses Server-Side Rendering (SSR) untuk area di dalam tag tersebut. Nuxt tidak akan memproses inisialisasi peta di server Node.js, menghindarkan aplikasi dari error fatal *"window is not defined"*.
*   **Visual saat lambat**: Selama browser client mengunduh modul Javascript Leaflet yang berat, Nuxt akan menggambar visual cadangan yang diletakkan di dalam tag `<template #fallback>`, yaitu kotak loading abu-abu bertuliskan *"⏳ Mempersiapkan visualisasi peta..."* agar layar tidak tampak kosong rusak.

### Jawaban 3: Pentingnya Tinggi CSS pada Kontainer Peta
*   **Aturan Render Leaflet**: Leaflet menggambar ratusan kepingan gambar peta (*Tiles*) di dalam kontainer HTML. 
*   Jika kontainer `div` tersebut tidak disetel ukuran tingginya secara eksplisit (tinggi default bernilai `0` pixel), Leaflet tidak akan memiliki ruang untuk menggambar gambar peta tersebut. Akibatnya, peta Anda akan **tampak tidak terlihat/gaib** di layar peramban meskipun server menyatakan inisialisasi sukses tanpa error.
