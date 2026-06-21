# Hari 67: Peta Distribusi Logistik UDD PMI
## Menghubungkan API Koordinat Cabang UDD PMI Dengan Kerangka Peta Leaflet Dinamis

Selamat datang di Hari 67! Hari ini kita akan mempelajari cara memetakan koordinat geolokasi menggunakan integrasi **API Peta Distribusi** di Nuxt 3.

Di dalam sistem logistik PMI, pimpinan dinas membutuhkan visualisasi sebaran lokasi rumah sakit swasta penerima donor dan titik kantor cabang UDD PMI di Lampung. Kita akan belajar cara mengambil daftar koordinat dari backend API (`GET /api/peta-cabang`) dan menyiapkan data spasial tersebut agar siap digambar di atas peta visual Leaflet JS di Hari 76.

---

## 1. Konsep Data Geografis (GeoJSON / Lintang Bujur)

Untuk menampilkan marker penunjuk arah di peta, server backend mengembalikan format koordinat GPS standar berupa lintang bujur (**Latitude / Longitude**):
*   **Latitude (Lintang)**: Garis horizontal bumi (bernilai negatif di belahan bumi selatan, seperti Lampung bernilai sekitar `-5.397`).
*   **Longitude (Bujur)**: Garis vertikal bumi (Lampung bernilai sekitar `105.266`).

---

## 2. Studi Kasus PMI: Mengambil Koordinat Cabang & Menata visual Card Lokasi

Mari kita buat halaman pratinjau koordinat wilayah sebelum kita memasang library peta fisik Leaflet di Fase 5.

### Berkas Halaman: `pages/peta-distribusi.vue`

```html
<!-- pages/peta-distribusi.vue -->
<template>
  <div class="space-y-6">
    <div>
      <h2 class="text-xl font-bold text-slate-800">📍 Peta Sebaran Cabang UDD</h2>
      <p class="text-xs text-slate-500 mt-1">Daftar koordinat GPS aktif kantor UDD PMI dan Rumah Sakit Mitra Lampung</p>
    </div>

    <!-- Alarm Error -->
    <div v-if="error" class="p-4 bg-red-50 text-red-800 text-xs rounded-xl">
      🚨 Gagal memuat data koordinat: {{ error.message }}
    </div>

    <!-- Grid List Lokasi -->
    <div v-if="pending" class="grid grid-cols-1 md:grid-cols-3 gap-6 animate-pulse">
      <div v-for="i in 3" :key="i" class="h-32 bg-slate-200 rounded-3xl"></div>
    </div>

    <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-6">
      <!-- Card Marker Koordinat -->
      <div 
        v-for="lokasi in daftarLokasi" 
        :key="lokasi.id" 
        class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4 hover:shadow-md transition-shadow"
      >
        <div class="flex justify-between items-start">
          <span class="text-lg">🏢</span>
          <span 
            class="px-2.5 py-0.5 rounded-full text-[9px] font-bold uppercase tracking-wider"
            :class="lokasi.kategori === 'UDD' ? 'bg-red-50 text-red-600 border border-red-100' : 'bg-blue-50 text-blue-600 border border-blue-100'"
          >
            {{ lokasi.kategori }}
          </span>
        </div>

        <div>
          <h4 class="font-bold text-slate-800 text-sm">{{ lokasi.nama }}</h4>
          <p class="text-xs text-slate-400 mt-1">Alamat: {{ lokasi.alamat }}</p>
        </div>

        <!-- Tampilan Koordinat GPS -->
        <div class="pt-3 border-t border-slate-100 flex justify-between text-[10px] font-mono text-slate-500">
          <span>Lat: {{ lokasi.latitude }}</span>
          <span>Lng: {{ lokasi.longitude }}</span>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

// API Tiruan berisi data koordinat UDD PMI Lampung
const API_MAP_URL = 'https://pmi-backend-api.onrender.com/api/donations';

// Ambil data koordinat
const { data: rawData, pending, error } = await useFetch(API_MAP_URL, {
  lazy: true
});

// Computed menyaring data agar memiliki format koordinat standar Lampung
const daftarLokasi = computed(() => {
  if (!rawData.value) return [];
  
  // Memetakan data dari database PMI ke format siap tempel marker GPS
  return rawData.value.map((item, index) => ({
    id: item.id || index,
    nama: item.tempat || 'UDD PMI Cabang',
    alamat: item.alamat || 'Provinsi Lampung',
    kategori: index % 2 === 0 ? 'UDD' : 'RS MITRA',
    // Koordinat simulasi seputar Lampung
    latitude: -5.397 + (index * 0.05), 
    longitude: 105.266 - (index * 0.02)
  }));
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati bagaimana data `latitude` dan `longitude` dihitung secara dinamis di atas.
2. Jelaskan mengapa tipe data untuk koordinat GPS (`latitude` dan `longitude`) wajib bertipe data **Float/Number** desimal, bukan bertipe **String** saat nantinya diplot ke dalam API Leaflet Map.
3. Sebutkan kelebihan visual dari penggunaan ikon penanda (*Marker*) yang dibedakan warnanya berdasarkan kategori instansi (UDD PMI merah vs RS Mitra biru) bagi kenyamanan mata pimpinan.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya Tipe Data Koordinat Float
*   **Wajib Float**: Library pembuat peta (seperti Leaflet JS atau Google Maps API) menggunakan koordinat sebagai parameter perhitungan jarak radius matematika di atas permukaan bumi. 
*   **Akibat jika String**: Jika kita mengirimkan koordinat berbentuk string (contoh: `latitude: "-5.39"`), library peta akan melempar pesan error crash di konsol log browser karena tidak bisa menjumlahkan string teks dengan angka desimal, menyebabkan marker gagal tergambar atau terplot melayang di antah berantah koordinat `0,0` (Null Island).

### Jawaban 3: Manfaat Kategorisasi Warna Marker
*   **Penyaringan Visual Instan (Cognitive Load Reduction)**: Pimpinan dinas tidak perlu mengklik marker satu per satu hanya untuk membedakan mana kantor cabang UDD PMI penyuplai darah dan mana Rumah Sakit Swasta peminta darah. 
*   Perbedaan warna (merah vs biru) memberikan pemahaman spasial instan dalam 1 detik pandangan layar pertama, mempercepat proses pengambilan keputusan logistik distribusi darah.
