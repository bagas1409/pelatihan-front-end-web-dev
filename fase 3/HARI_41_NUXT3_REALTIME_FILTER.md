# Hari 41: Filter Pencarian Real-time di Client
## Menggabungkan Input Filter dengan Computed Property untuk Menyaring Baris Tabel Logistik Instan

Selamat datang di Hari 41! Hari ini kita akan mempelajari cara menyusun fitur **Pencarian & Penyaringan Real-time** (Real-time Search Filter) di sisi client.

Pada dashboard admin PMI, database kita memuat daftar persediaan darah dari berbagai wilayah cabang UDD. Kita akan merancang filter interaktif di mana petugas cukup mengetik nama wilayah atau mengklik tombol kategori golongan darah, dan baris-baris tabel akan langsung tersaring menyusut dalam hitungan milidetik tanpa perlu membebani server backend untuk memuat ulang data.

---

## 1. Konsep Filter di Sisi Client (Client-Side Filtering)

Sistem filter client yang responsif memadukan tiga komponen Vue 3:
1.  **State Data Asli (Array)**: Menyimpan kumpulan data lengkap hasil fetch API server (tidak boleh diubah agar kita tidak kehilangan data cadangan asli).
2.  **State Filter (Input Variabel)**: Variabel reaktif (`ref`) yang diikat ke kolom input teks pencarian (`v-model`).
3.  **Computed Property (Array Hasil Saringan)**: Fungsi pintar yang memproses penyaringan data asli berdasarkan isi keyword pencarian saat itu juga. Di HTML, kita melakukan perulangan `v-for` menggunakan array computed ini, bukan array asli.

---

## 2. Studi Kasus PMI: Tabel Logistik dengan Filter Golongan & Lokasi

Berikut adalah kode lengkap halaman persediaan logistik UDD PMI yang memiliki filter pencarian lokasi dan filter tombol kategori golongan darah.

```html
<!-- pages/logistik-filter.vue -->
<template>
  <div class="max-w-xl mx-auto p-6 bg-slate-50 min-h-screen space-y-6">
    <div>
      <h2 class="text-xl font-bold text-slate-800">🔍 Filter Cepat Logistik</h2>
      <p class="text-xs text-slate-500 mt-1">Saring stok darah berdasarkan wilayah UDD cabang dan golongan darah</p>
    </div>

    <!-- AREA KENDALI FILTER -->
    <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
      
      <!-- 1. INPUT PENCARIAN TEKS WILAYAH -->
      <div>
        <label class="block text-xs font-bold text-slate-400 uppercase mb-2">Cari Wilayah Cabang</label>
        <input 
          type="text" 
          v-model="searchQuery" 
          placeholder="Ketik nama kota (contoh: Metro, Lamsel)..." 
          class="w-full px-4 py-3 rounded-xl border border-slate-200 outline-none focus:border-red-500 focus:ring-4 focus:ring-red-100 text-sm transition-all"
        />
      </div>

      <!-- 2. TOMBOL FILTER GOLONGAN DARAH -->
      <div>
        <label class="block text-xs font-bold text-slate-400 uppercase mb-2">Filter Golongan Darah</label>
        <div class="flex gap-2 flex-wrap">
          <button 
            @click="selectedGol = 'Semua'"
            class="px-4 py-2 rounded-xl text-xs font-bold transition-all"
            :class="selectedGol === 'Semua' ? 'bg-red-500 text-white shadow-md' : 'bg-slate-100 text-slate-600 hover:bg-slate-200'"
          >
            Semua
          </button>
          <button 
            v-for="gol in ['A', 'B', 'AB', 'O']" 
            :key="gol"
            @click="selectedGol = gol"
            class="px-4 py-2 rounded-xl text-xs font-bold transition-all"
            :class="selectedGol === gol ? 'bg-red-500 text-white shadow-md' : 'bg-slate-100 text-slate-600 hover:bg-slate-200'"
          >
            Gol {{ gol }}
          </button>
        </div>
      </div>

    </div>

    <!-- TABEL HASIL SARINGAN -->
    <div class="bg-white rounded-3xl border border-slate-200 shadow-sm overflow-hidden">
      <div class="p-6 border-b border-slate-100 flex justify-between items-center bg-slate-50/50">
        <h3 class="font-bold text-slate-800 text-sm">Daftar Hasil Saringan</h3>
        <span class="text-xs bg-slate-100 text-slate-500 px-3 py-1 rounded-full font-bold">
          {{ hasilSaringan.length }} Data Cocok
        </span>
      </div>

      <ul v-if="hasilSaringan.length > 0" class="divide-y divide-slate-100 text-sm p-6">
        <li v-for="item in hasilSaringan" :key="item.id" class="py-3 flex justify-between items-center">
          <div>
            <p class="font-bold text-slate-800">Golongan {{ item.golongan }}</p>
            <p class="text-xs text-slate-400 mt-0.5">Region: {{ item.region }}</p>
          </div>
          <span class="font-mono font-bold text-slate-900 bg-slate-100 px-3 py-1 rounded-xl">
            {{ item.jumlah }} Bag
          </span>
        </li>
      </ul>

      <!-- Jika data filter kosong -->
      <div v-else class="text-center py-12 text-slate-400 text-sm">
        🔍 Tidak ada persediaan darah yang cocok dengan kriteria filter.
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

// 1. DATA ASLI DARI API
const databaseStok = ref([
  { id: 1, golongan: "O", jumlah: 98, region: "Bandar Lampung" },
  { id: 2, golongan: "A", jumlah: 12, region: "Bandar Lampung" },
  { id: 3, golongan: "B", jumlah: 45, region: "Metro" },
  { id: 4, golongan: "AB", jumlah: 4, region: "Lampung Selatan" },
  { id: 5, golongan: "O", jumlah: 22, region: "Metro" },
  { id: 6, golongan: "A", jumlah: 3, region: "Lampung Selatan" }
]);

// 2. STATE KENDALI FILTER
const searchQuery = ref('');
const selectedGol = ref('Semua');

// 3. COMPUTED: MEMPROSES PENYARINGAN DINAMIS
const hasilSaringan = computed(() => {
  return databaseStok.value.filter(item => {
    
    // A. Cek kecocokan ketikan wilayah (Case-Insensitive)
    const cocokWilayah = item.region.toLowerCase().includes(searchQuery.value.toLowerCase());
    
    // B. Cek kecocokan pilihan golongan darah
    const cocokGol = selectedGol.value === 'Semua' || item.golongan === selectedGol.value;
    
    // Data lolos saringan jika memenuhi kedua kriteria di atas
    return cocokWilayah && cocokGol;
  });
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati parameter pencarian: `item.region.toLowerCase().includes(searchQuery.value.toLowerCase())`.
2. Jelaskan fungsi dari pemanggilan metode JavaScript `.toLowerCase()` pada perbandingan string di atas dan apa akibat buruknya bagi pengguna jika kita lupa menyisipkannya.
3. Sebutkan kelemahan jika kita menggunakan penyaringan data di sisi client (*Client-Side Filtering*) jika jumlah data yang di-render di tabel mencapai **100.000 baris data**. Tentukan solusi alternatif penanganannya.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya Case-Insensitive String
*   **Fungsi `.toLowerCase()`**: Mengubah semua karakter huruf di dalam teks menjadi huruf kecil polos.
*   **Akibat jika dihapus**: Pencarian akan bersifat peka huruf besar-kecil (*Case-Sensitive*). Jika petugas mengetik kata `"metro"` (huruf kecil) di kolom input pencarian, browser tidak akan menemukan data wilayah `"Metro"` (huruf kapital) karena huruf `"m"` kecil tidak dianggap sama dengan `"M"` kapital oleh JavaScript. Ini membuat filter terasa tidak akurat bagi pengguna biasa.

### Jawaban 3: Batasan Filter Client & Solusi Server Filter
*   **Kelemahan Data Besar**: Jika data mencapai 100.000 baris, memuat seluruh data ke memori RAM browser client akan membuat browser mematung macet (*Out of Memory*), dan fungsi `.filter()` akan memakan waktu komputasi CPU yang sangat lama di handphone pengguna.
*   **Solusi Alternatif (Server-Side Filtering)**: Kita memindahkan filter ke database backend menggunakan query params API: `GET /api/volunteers?search=metro&page=1&limit=20`. Frontend hanya bertugas mengirim keyword pencarian yang diketik user ke API, dan server backend mengembalikan data yang sudah tersaring dalam bentuk potongan halaman kecil (*Pagination*).
