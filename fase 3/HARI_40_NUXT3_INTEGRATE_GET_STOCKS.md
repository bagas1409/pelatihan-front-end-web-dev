# Hari 40: Integrasi API Get Stok Darah
## Menghubungkan Nuxt 3 dengan Server Backend Express.js & Merender Persediaan Darah UDD Riil

Selamat datang di Hari 40! Hari ini kita akan melakukan integrasi nyata pertama dengan menghubungkan aplikasi frontend **Nuxt 3** kita dengan **Server API Backend Express.js** yang sesungguhnya.

Kita akan memanggil data persediaan kantong darah dari database server (`GET /api/stocks`), menatanya menggunakan Tailwind CSS, dan menyajikan tabel laporan yang rapi di layar monitor dashboard admin DonorKu.

---

## 1. Menentukan URL Server API Backend

Pastikan server API backend Express Anda sudah aktif menyala (misal di server lokal: `http://localhost:5000/api` atau di server cloud Render: `https://pmi-backend-api.onrender.com/api`). 

*Catatan: Jika menembak server lokal dari browser, pastikan backend Anda sudah menyalakan konfigurasi **CORS** (Cross-Origin Resource Sharing) agar browser client tidak memblokir request data.*

---

## 2. Struktur Kode Integrasi Halaman Stok Darah

Mari kita buat halaman kelola stok darah dinamis di folder `pages/stok.vue`:

```html
<!-- pages/stok.vue -->
<template>
  <div class="space-y-6">
    
    <!-- HEADER HALAMAN -->
    <div class="flex justify-between items-center border-b pb-4 border-slate-200">
      <div>
        <h2 class="text-xl font-bold text-slate-800">🩸 Persediaan Darah UDD</h2>
        <p class="text-xs text-slate-500 mt-1">
          Daftar kantong darah ter-update di kulkas penyimpanan medis UDD Lampung
        </p>
      </div>
      <!-- Tombol Refresh Data -->
      <button 
        @click="refresh" 
        :disabled="pending"
        class="bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-xs px-4 py-2 rounded-xl transition-all disabled:opacity-50"
      >
        {{ pending ? '⏳ Memuat...' : '🔄 Sinkronkan Data' }}
      </button>
    </div>

    <!-- AREA ALARM ERROR KONEKSI -->
    <div v-if="error" class="p-4 bg-red-50 border border-red-200 rounded-2xl flex items-center gap-3">
      <span class="text-xl">🚨</span>
      <div class="text-xs text-red-800">
        <p class="font-bold">Gagal terhubung ke database server!</p>
        <p class="mt-0.5">{{ error.message }}</p>
      </div>
    </div>

    <!-- AREA UTAMA TABEL PERSERDIAN -->
    <div class="bg-white rounded-3xl border border-slate-200 shadow-sm overflow-hidden">
      <table class="w-full text-left border-collapse">
        <thead class="bg-slate-50 border-b border-slate-100 text-xs font-bold text-slate-500 uppercase tracking-wider">
          <tr>
            <th class="py-4 px-6">Golongan Darah</th>
            <th class="py-4 px-6 text-center">Jumlah Kantong</th>
            <th class="py-4 px-6">Status Logistik</th>
            <th class="py-4 px-6">Region</th>
          </tr>
        </thead>
        
        <!-- Render Loading Placeholder jika data belum siap -->
        <tbody v-if="pending && !daftarStok">
          <tr v-for="i in 4" :key="i" class="border-b border-slate-100 animate-pulse">
            <td class="py-4 px-6"><div class="h-4 bg-slate-100 rounded w-16"></div></td>
            <td class="py-4 px-6"><div class="h-4 bg-slate-100 rounded w-12 mx-auto"></div></td>
            <td class="py-4 px-6"><div class="h-4 bg-slate-100 rounded w-24"></div></td>
            <td class="py-4 px-6"><div class="h-4 bg-slate-100 rounded w-20"></div></td>
          </tr>
        </tbody>

        <!-- Render Baris Data Asli -->
        <tbody v-else class="text-sm divide-y divide-slate-100">
          <tr v-for="item in daftarStok" :key="item.id" class="hover:bg-slate-50/50 transition-colors">
            
            <!-- Golongan Darah -->
            <td class="py-4 px-6 font-bold text-slate-900">
              Golongan {{ item.golongan }}
            </td>
            
            <!-- Jumlah Kantong -->
            <td class="py-4 px-6 text-center font-mono font-bold text-slate-800">
              {{ item.jumlah }} Bag
            </td>
            
            <!-- Status Logistik (Warna Dinamis berdasarkan jumlah) -->
            <td class="py-4 px-6">
              <span 
                class="px-3 py-1 rounded-full text-[10px] font-bold uppercase tracking-wider"
                :class="item.jumlah < 15 
                  ? 'bg-red-50 text-red-600 border border-red-100' 
                  : 'bg-emerald-50 text-emerald-600 border border-emerald-100'"
              >
                {{ item.jumlah < 15 ? '🚨 Kritis (Segera CITO)' : '✓ Persediaan Aman' }}
              </span>
            </td>

            <!-- Region -->
            <td class="py-4 px-6 text-slate-500 font-medium">
              {{ item.region || 'UDD Pusat' }}
            </td>

          </tr>
        </tbody>
      </table>
    </div>

  </div>
</template>

<script setup>
// Menyuruh halaman ini menggunakan layout dashboard default (ada sidebar)
definePageMeta({
  layout: 'default'
});

// Ganti URL ini dengan URL server API backend Express riil Anda
const API_URL = 'https://pmi-backend-api.onrender.com/api/stocks';

// Ambil data dengan useFetch
const { data: daftarStok, pending, error, refresh } = await useFetch(API_URL, {
  lazy: true
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati kode visualisasi status logistik di atas: `item.jumlah < 15`.
2. Jika server backend Express Anda mengembalikan struktur data JSON yang berbeda nama kolomnya (misal kolom `golongan` diganti nama menjadi `blood_type`), sebutkan baris kode HTML bagian mana saja yang wajib disunting agar aplikasi web tidak error menampilkan kolom kosong.
3. Mengapa kita disarankan memisahkan alamat base URL API ke dalam variabel konfigurasi seperti `const API_URL` dibandingkan menuliskan URL mentah berulang kali langsung di dalam kurung parameter `useFetch('https://...')`?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Menangani Perubahan Struktur JSON Backend
Jika kolom JSON diubah menjadi `blood_type`, kita wajib menyunting bagian rendering objek di dalam perulangan `v-for` di HTML:
```html
<!-- Baris lama -->
Golongan {{ item.golongan }}

<!-- Baris baru yang disunting -->
Golongan {{ item.blood_type }}
```
Hal ini membuktikan bahwa tim frontend dan tim backend wajib bersepakat melahirkan kontrak data (API Contract) sebelum mulai mengoding agar integrasi data berjalan mulus tanpa salah kunci variabel.

### Jawaban 3: Kegunaan Base URL Variable
*   **Kemudahan Maintenance**: Selama tahap pengembangan (development), kita menembak server lokal `http://localhost:5000`. Saat website siap dirilis (production), kita harus menembak server cloud asli `https://api.pmilampung.org`.
*   Jika kita menulis URL mentah di 20 file halaman yang berbeda, kita harus membuka ke-20 file tersebut dan mengubah tautan satu per satu secara manual (rawan terlewat). Dengan menyimpan alamat di variabel tunggal (atau file `.env`), kita hanya perlu menyunting alamat di 1 baris kode saja untuk merubah seluruh target koneksi server website secara serentak.
```
[FASE 3: INTEGRASI PEMBUKA DATA API BACKEND SUKSES]
```
