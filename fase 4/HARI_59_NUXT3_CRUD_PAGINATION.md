# Hari 59: Integrasi CRUD Relawan & Pagination
## Menyajikan Tabel Database Relawan PMI Dilengkapi Navigasi Halaman Maju-Mundur

Selamat datang di Hari 59! Hari ini kita akan mempelajari cara membangun **Tabel CRUD Terpaginasi** (Paginated CRUD Table) untuk menyajikan data database relawan PMI Lampung secara profesional.

Jika database kita menampung ribuan data relawan, menampilkannya sekaligus dalam satu halaman akan membuat loading website terasa sangat lambat dan memakan kuota internet. Kita akan memecah data tersebut menjadi halaman-halaman kecil (seperti hanya menampilkan 5 data per halaman) dan membuat navigasi tombol angka halaman (*Pagination*) menggunakan request query dinamis ke API backend.

---

## 1. Konsep Pagination di Sisi Server (Server-Side Pagination)

Untuk meminta data halaman spesifik, frontend mengirimkan dua parameter koordinat query lewat URL API:
*   `page`: Halaman aktif saat ini (contoh: `page=1` atau `page=2`).
*   `limit`: Jumlah baris data maksimal per halaman (contoh: `limit=5`).
*   *Reaktivitas*: Kita mengikat variabel `page` ke dalam parameter query `useApi`. Setiap kali variabel `page` diubah nilainya oleh tombol navigasi, Nuxt secara otomatis mendeteksi perubahan tersebut dan memicu pengambilan data ulang ke backend.

---

## 2. Studi Kasus PMI: Tabel Relawan Terpaginasi Lengkap

Mari kita buat halaman daftar relawan di berkas **`pages/relawan/index.vue`** menggunakan custom fetch `useApi` yang dinamis memantau halaman aktif.

```html
<!-- pages/relawan/index.vue -->
<template>
  <div class="space-y-6">
    
    <!-- Header Halaman -->
    <div class="flex justify-between items-center border-b pb-4">
      <div>
        <h2 class="text-xl font-bold text-slate-800">👥 Database Relawan PMI</h2>
        <p class="text-xs text-slate-500 mt-1">Daftar seluruh pendonor aktif terdaftar di provinsi Lampung</p>
      </div>
      <NuxtLink to="/relawan/baru" class="bg-red-500 hover:bg-red-600 text-white font-bold text-xs px-4 py-2.5 rounded-xl transition-all">
        + Registrasi Baru
      </NuxtLink>
    </div>

    <!-- Alarm Status API -->
    <div v-if="error" class="p-4 bg-red-50 text-red-800 text-xs rounded-2xl">
      🚨 Gagal memuat data relawan: {{ error.message }}
    </div>

    <!-- Tabel Data -->
    <div class="bg-white rounded-3xl border border-slate-200 shadow-sm overflow-hidden">
      <table class="w-full text-left border-collapse">
        <thead class="bg-slate-50 border-b border-slate-100 text-xs font-bold text-slate-500 uppercase tracking-wider">
          <tr>
            <th class="py-4 px-6">Nama Relawan</th>
            <th class="py-4 px-6 text-center">Golongan Darah</th>
            <th class="py-4 px-6">Region</th>
            <th class="py-4 px-6 text-right">Aksi</th>
          </tr>
        </thead>

        <!-- Tampilan Loading -->
        <tbody v-if="pending" class="animate-pulse">
          <tr v-for="i in 3" :key="i" class="border-b">
            <td class="py-4 px-6"><div class="h-4 bg-slate-100 rounded w-24"></div></td>
            <td class="py-4 px-6"><div class="h-4 bg-slate-100 rounded w-10 mx-auto"></div></td>
            <td class="py-4 px-6"><div class="h-4 bg-slate-100 rounded w-20"></div></td>
            <td class="py-4 px-6"><div class="h-4 bg-slate-100 rounded w-16 ml-auto"></div></td>
          </tr>
        </tbody>

        <!-- Tampilan Sukses Merender Data -->
        <tbody v-else class="text-sm divide-y divide-slate-100">
          <tr v-for="relawan in responApi?.data" :key="relawan.id" class="hover:bg-slate-50/50">
            <td class="py-4 px-6 font-bold text-slate-800">{{ relawan.nama }}</td>
            <td class="py-4 px-6 text-center font-bold text-red-500">{{ relawan.golongan }}</td>
            <td class="py-4 px-6 text-slate-500">{{ relawan.region || 'Lampung' }}</td>
            <td class="py-4 px-6 text-right space-x-2">
              <NuxtLink :to="`/relawan/${relawan.id}`" class="text-xs text-blue-500 font-bold hover:underline">
                Profil
              </NuxtLink>
            </td>
          </tr>
        </tbody>
      </table>

      <!-- AREA TOMBOL PAGINATION -->
      <div v-if="responApi" class="p-6 border-t border-slate-100 flex justify-between items-center bg-slate-50/50">
        <span class="text-xs text-slate-400 font-medium">
          Halaman <strong>{{ HalamanAktif }}</strong> dari <strong>{{ responApi.totalPages || 1 }}</strong> Halaman
        </span>

        <div class="flex gap-2 text-xs font-bold">
          <!-- Tombol Mundur -->
          <button 
            @click="kembaliHalaman" 
            :disabled="HalamanAktif === 1 || pending"
            class="px-3 py-1.5 border rounded-lg bg-white disabled:opacity-40 hover:bg-slate-50"
          >
            &larr; Sebelumnya
          </button>
          
          <!-- Tombol Maju -->
          <button 
            @click="majuHalaman" 
            :disabled="HalamanAktif >= (responApi.totalPages || 1) || pending"
            class="px-3 py-1.5 border rounded-lg bg-white disabled:opacity-40 hover:bg-slate-50"
          >
            Selanjutnya &rarr;
          </button>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

const HalamanAktif = ref(1);
const limitData = 5; // Tampilkan 5 data per halaman

// Memanggil useApi dengan menyematkan query halaman aktif
// watch: [HalamanAktif] -> Memicu useApi meng-fetch ulang otomatis jika HalamanAktif berubah nilainya!
const { data: responApi, pending, error } = await useApi('/volunteers', {
  query: {
    page: HalamanAktif,
    limit: limitData
  },
  watch: [HalamanAktif]
});

const kembaliHalaman = () => {
  if (HalamanAktif.value > 1) {
    HalamanAktif.value--;
  }
};

const majuHalaman = () => {
  if (HalamanAktif.value < responApi.value.totalPages) {
    HalamanAktif.value++;
  }
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati opsi `watch: [HalamanAktif]` pada inisialisasi `useApi` di atas.
2. Jelaskan fungsi dari penyertaan parameter opsi `watch` tersebut dan apa akibat buruknya bagi visualisasi data jika kita lupa memasangnya saat tombol "Selanjutnya" diklik oleh petugas admin.
3. Sebutkan struktur respons JSON yang biasanya wajib dikirim oleh server backend agar tombol navigasi pagination di atas bisa mengunci mati tombol "Selanjutnya" di akhir halaman secara akurat.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya Opsi Watch untuk Fetching
*   **Fungsi opsi `watch`**: Memberi instruksi kepada Nuxt 3 untuk selalu memantau variabel state di dalam array tersebut. Kapanpun nilai `HalamanAktif` berubah (misal dari `1` ke `2`), Nuxt akan segera menembak ulang API ke backend dengan query terbaru (`?page=2`).
*   **Akibat jika dihapus**: Saat tombol diklik, angka variabel `HalamanAktif` memang akan berubah naik menjadi `2` di memori RAM. Namun, karena Nuxt tidak diajarkan untuk memantau variabel tersebut, **tabel visual akan tetap menampilkan data halaman 1** karena browser tidak pernah menembak ulang API dengan rute baru.

### Jawaban 3: Struktur Respons Meta Pagination Backend
Backend wajib menyuplai properti metadata pendukung di dalam JSON responsnya selain data array utama:
```json
{
  "data": [ ... daftar relawan ... ],
  "currentPage": 1,
  "totalPages": 4,
  "totalItems": 20
}
```
Properti **`totalPages`** sangat krusial karena digunakan oleh frontend untuk mengevaluasi kondisi batas akhir `:disabled="HalamanAktif >= responApi.totalPages"`, mencegah pengguna mengklik maju terus menerus melewati batas halaman data asli yang ada di database.
