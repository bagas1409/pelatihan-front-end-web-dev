# Hari 77: Kustomisasi Marker Peta Leaflet.js
## Membuat Penanda Koordinat Kustom Berwarna Merah Untuk Kantor PMI & Biru Untuk Rumah Sakit

Selamat datang di Hari 77! Hari ini kita akan mempelajari cara memasang **Marker** (Penanda Lokasi) kustom di atas peta Leaflet.js kita.

Secara default, Leaflet menggunakan ikon pin berwarna biru polos untuk menandai semua titik koordinat. Pada sistem logistik PMI Lampung, kita harus membedakan secara visual:
*   Ikon penanda **Merah** $\rightarrow$ Menandai lokasi kantor cabang UDD PMI penyuplai darah.
*   Ikon penanda **Biru/Hijau** $\rightarrow$ Menandai lokasi Rumah Sakit Mitra peminta pasokan darah.
Kita akan belajar cara membuat ikon marker dinamis menggunakan gambar lokal yang ditaruh di folder `public/`.

---

## 1. Menentukan File Gambar Marker Kustom

Pastikan Anda memiliki dua gambar ikon format PNG transparan di folder **`public/images/`** Anda (misalnya `public/images/marker-pmi.png` dan `public/images/marker-rs.png`). Jika belum ada, Anda bisa menggunakan generator gambar atau URL gambar statis sementara.

---

## 2. Studi Kasus PMI: Menggambar Marker Berbeda Kategori

Mari kita modifikasi file peta kita agar secara dinamis memetakan daftar koordinat dari API dan memasang ikon marker yang sesuai kategori.

Buka kembali berkas halaman **`pages/peta/index.vue`** Anda, lalu sesuaikan blok skripnya:

```html
<!-- pages/peta/index.vue (Versi Lengkap dengan Custom Marker) -->
<template>
  <div class="space-y-6">
    <div>
      <h2 class="text-xl font-bold text-slate-800">🗺️ Peta Monitor Sebaran UDD</h2>
      <p class="text-xs text-slate-500 mt-1">Titik Merah: Kantor UDD PMI, Titik Biru: RS Swasta</p>
    </div>

    <ClientOnly>
      <div class="bg-white p-4 rounded-3xl border border-slate-200 shadow-sm overflow-hidden">
        <div id="map-kontainer" class="h-[400px] w-full rounded-2xl z-10"></div>
      </div>
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

// Data koordinat UDD PMI dan RS Mitra Lampung
const daftarKordinat = [
  { nama: 'UDD PMI Lampung Pusat', lat: -5.397, lng: 105.266, tipe: 'PMI' },
  { nama: 'RS Urip Sumoharjo', lat: -5.385, lng: 105.285, tipe: 'RS' },
  { nama: 'RSU Abdul Moeloek', lat: -5.405, lng: 105.245, tipe: 'RS' }
];

import 'leaflet/dist/leaflet.css';

onMounted(async () => {
  const L = await import('leaflet');

  // Inisialisasi peta dasar Lampung
  mapInstance = L.map('map-kontainer').setView([-5.397, 105.266], 13);

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; OpenStreetMap contributors'
  }).addTo(mapInstance);

  // 1. BUAT IKON KUSTOM MERAH (PMI)
  const ikonPmi = L.icon({
    iconUrl: 'https://cdn-icons-png.flaticon.com/512/809/809957.png', // Contoh URL icon donor merah
    iconSize: [38, 38], // Ukuran lebar tinggi gambar marker [lebar, tinggi]
    iconAnchor: [19, 38], // Titik jangkar penempelan koordinat tepat di ujung bawah jarum
    popupAnchor: [0, -35] // Mengatur letak munculnya kotak tulisan balon di atas kepala marker
  });

  // 2. BUAT IKON KUSTOM BIRU (RUMAH SAKIT)
  const ikonRs = L.icon({
    iconUrl: 'https://cdn-icons-png.flaticon.com/512/2928/2928158.png', // Contoh URL icon gedung RS biru
    iconSize: [38, 38],
    iconAnchor: [19, 38],
    popupAnchor: [0, -35]
  });

  // 3. LOOPING MENGGAMBAR MARKER DI PETA
  daftarKordinat.forEach(titik => {
    // Pilih ikon berdasarkan kategori tipe data
    const ikonPilihan = titik.tipe === 'PMI' ? ikonPmi : ikonRs;

    // Gambar marker ke koordinat GPS bersangkutan
    L.marker([titik.lat, titik.lng], { icon: ikonPilihan })
     .addTo(mapInstance);
  });
});

onUnmounted(() => {
  if (mapInstance) {
    mapInstance.remove();
  }
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati parameter pembuatan icon kustom: `iconAnchor: [19, 38]`.
2. Jelaskan fungsi dari parameter **`iconAnchor`** pada koordinat marker Leaflet dan apa akibat buruknya bagi keakuratan peta jika kita salah mengisi koordinat jangkar tersebut.
3. Sebutkan kelebihan visual dari penggunaan ikon kustom berbentuk simbol instansi (seperti logo PMI merah) dibandingkan menggunakan marker balon standar bawaan Leaflet.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya Koordinat Jangkar (Icon Anchor)
*   **Fungsi `iconAnchor`**: Menentukan titik kordinat piksel pada gambar PNG yang bertindak sebagai **ujung lancip jarum penunjuk lokasi**. Jika ukuran gambar adalah 38x38 piksel, maka titik lancip tengah bawah berada di koordinat `[lebar / 2, tinggi]`, yaitu `[19, 38]`.
*   **Akibat jika salah**: Jika kita mengosongkannya (default `[0,0]`), Leaflet akan menaruh titik tengah koordinat GPS tepat di pojok kiri atas gambar ikon. Saat pengguna melakukan zoom-in dan zoom-out, gambar marker akan **tampak bergeser melayang-layang** menjauhi jalan aslinya di peta, menyebabkan kesalahan navigasi lokasi bagi petugas kurir darah.

### Jawaban 3: Keunggulan Simbol Kustom Instansi
*   **Kejelasan Informasi Cepat (Cognitive Ease)**: Tanpa perlu mengklik penanda, petugas dinas logistik langsung mengenali sebaran titik hanya dari sekilas melihat gambar gedung rumah sakit biru dan logo PMI merah di atas wilayah peta Lampung, mempercepat analisis distribusi logistik.
