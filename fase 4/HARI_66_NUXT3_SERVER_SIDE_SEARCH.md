# Hari 66: Pencarian di Sisi Server (Server-Side Search)
## Menangani Pencarian Database Relawan Besar Secara Real-time Tanpa Membebani Memori Browser

Selamat datang di Hari 66! Hari ini kita akan mempelajari cara membangun fitur **Pencarian di Sisi Server** (Server-Side Search) untuk menangani database relawan berskala besar di PMI Lampung.

Di Hari 41 kita sudah mempelajari filter pencarian di sisi client. Namun, jika jumlah relawan terdaftar mencapai puluhan ribu orang, browser client tidak sanggup mengunduh semua data tersebut sekaligus. Solusinya, kita harus mengirimkan kata kunci pencarian ke API backend (`GET /api/volunteers?search=budi`), membiarkan database server mencari data yang cocok, lalu mengembalikan hasilnya dalam jumlah kecil yang responsif.

---

## 1. Konsep Debouncing pada Pencarian Server

Saat kita mengetik di kolom pencarian server, ada satu masalah UX:
*   Jika kita mengetik kata `"SITI"`, browser akan mengirimkan request HTTP sebanyak 4 kali berturut-turut untuk setiap huruf yang diketik: `"S"`, `"SI"`, `"SIT"`, lalu `"SITI"`.
*   Ini memboroskan beban server API dan memicu perang lalu lintas internet.
*   **Debouncing**: Teknik menunda pengiriman request API selama sekian milidetik (misalnya tunda 500ms). Jika pengguna masih terus mengetik, waktu tunggu direset. API baru akan ditembak setelah pengguna terdeteksi berhenti mengetik selama setengah detik.

---

## 2. Studi Kasus PMI: Halaman Database Relawan dengan Pencarian Server

Mari kita buat halaman pencarian relawan yang teringrasi penuh dengan query search ke API backend dan dilengkapi fitur debouncing sederhana di folder `pages/relawan/cari.vue`.

```html
<!-- pages/relawan/cari.vue -->
<template>
  <div class="max-w-xl mx-auto p-6 bg-slate-50 min-h-screen space-y-6">
    <div>
      <h2 class="text-xl font-bold text-slate-800">🔍 Cari Relawan UDD</h2>
      <p class="text-xs text-slate-500 mt-1">Pencarian server-side langsung ke database pusat PMI</p>
    </div>

    <!-- Kolom Input Pencarian -->
    <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm">
      <input 
        type="text" 
        v-model="keywordInput" 
        placeholder="Ketik nama relawan yang ingin dicari..." 
        class="w-full px-4 py-3 rounded-xl border border-slate-200 outline-none focus:border-red-500 text-sm transition-all focus:ring-4 focus:ring-red-100"
      />
      
      <!-- Indikator Status Pencarian -->
      <p v-if="pending" class="text-[10px] text-amber-500 font-bold mt-2 animate-pulse">
        ⏳ Mencari kecocokan di database server...
      </p>
      <p v-else-if="searchQuery" class="text-[10px] text-slate-400 font-bold mt-2">
        Menampilkan hasil pencarian untuk: "{{ searchQuery }}"
      </p>
    </div>

    <!-- Tabel Hasil Pencarian -->
    <div class="bg-white rounded-3xl border border-slate-200 shadow-sm overflow-hidden">
      <ul v-if="responApi?.data && responApi.data.length > 0" class="divide-y divide-slate-100 p-6 text-sm">
        <li v-for="item in responApi.data" :key="item.id" class="py-3 flex justify-between items-center">
          <div>
            <p class="font-bold text-slate-800">{{ item.nama }}</p>
            <p class="text-[10px] text-slate-400">NIK: {{ item.nik || '18710XXXXXXXXX' }}</p>
          </div>
          <span class="text-xs bg-red-50 text-red-500 font-bold px-3 py-1 rounded-xl">
            Golongan {{ item.golongan }}
          </span>
        </li>
      </ul>

      <!-- Pesan jika hasil pencarian kosong -->
      <div v-else class="text-center py-12 text-slate-400 text-sm">
        🕊️ Masukkan kata kunci pencarian di atas untuk memulai pencarian.
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, watch } from 'vue';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

// 1. STATE INPUT USER
const keywordInput = ref('');

// 2. STATE QUERY AKTIF (DITEMBAK KE API)
const searchQuery = ref('');

let debounceTimer = null;

// 3. DEBOUNCING: Pantau ketikan user, tunda update searchQuery selama 500ms
watch(keywordInput, (baru) => {
  if (debounceTimer) clearTimeout(debounceTimer);

  debounceTimer = setTimeout(() => {
    searchQuery.value = baru; // Update nilai searchQuery setelah user berhenti mengetik
  }, 500);
});

// 4. MEMANGGIL API SERVER-SIDE SEARCH
// watch: [searchQuery] -> Menembak API otomatis saat searchQuery diperbarui oleh debouncer
const { data: responApi, pending } = await useApi('/volunteers', {
  query: {
    search: searchQuery
  },
  watch: [searchQuery]
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati alur penundaan waktu di dalam watcher `keywordInput` di atas.
2. Jelaskan fungsi dari perintah `clearTimeout(debounceTimer)` pada baris pertama watcher dan apa akibat buruknya jika baris tersebut lupa kita sisipkan.
3. Sebutkan kelebihan pencarian di sisi server (*Server-Side Search*) dari segi performa baterai handphone dinas petugas lapangan.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan clearTimeout pada Debouncer
*   **Fungsi `clearTimeout()`**: Membatalkan rencana eksekusi timer yang sudah dijadwalkan sebelumnya.
*   **Akibat jika dihapus**: Timer lama akan tetap berjalan di memori browser. Jika Anda mengetik kata `"BUDI"` dengan cepat (dalam waktu 300ms), akan tercipta 4 timer yang mengantre bersamaan. Tanpa `clearTimeout()`, browser akan **tetap mengirimkan 4 request API beruntun ke server** sesaat setelah timer masing-masing huruf habis, membuat fungsi debouncing kita menjadi tidak berfungsi sama sekali.

### Jawaban 3: Keunggulan Server Search untuk Perangkat Mobile
*   **Hemat Daya Baterai & CPU**: Browser di HP petugas tidak perlu memproses filter teks, memilah array raksasa di RAM, atau memproses pencocokan string regex yang memakan tenaga processor. 
*   Beban komputasi dipindahkan sepenuhnya ke server backend database yang berspesifikasi tinggi. HP petugas hanya bertugas menerima paket data HTML kecil yang bersih dan langsung siap digambar di layar, menghemat penggunaan daya baterai HP dinas petugas lapangan PMI Lampung secara signifikan.
