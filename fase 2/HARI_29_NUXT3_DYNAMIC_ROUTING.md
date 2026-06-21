# Hari 29: Rute Dinamis (Dynamic Routing)
## Menangkap Parameter ID Relawan & Detail Event Menggunakan useRoute()

Selamat datang di Hari 29! Hari ini kita akan mempelajari cara menangani **Dynamic Routing** (Rute Dinamis) untuk membuat halaman detail spesifik di Nuxt 3.

Saat mengelola database PMI, kita tidak mungkin membuat file halaman manual untuk setiap anggota relawan (seperti `budi.vue`, `siti.vue`). Kita butuh membuat satu file halaman template dinamis yang bisa mendeteksi NIK KTP atau ID relawan dari alamat URL (misal: `/relawan/102`, `/relawan/105`), menarik data yang cocok dari database, lalu menampilkannya secara dinamis menggunakan composable **`useRoute()`**.

---

## 1. Aturan Penulisan Rute Dinamis di Nuxt 3

Untuk memberi tahu Nuxt bahwa bagian alamat URL adalah parameter dinamis variabel yang bisa berubah-ubah, kita membungkus nama file menggunakan tanda kurung siku siku **`[namaParameter].vue`**.

### Contoh Pemetaan Rute Dinamis:

| Susunan File Halaman | Alamat URL yang Diklik User | Nama Parameter yang Ditangkap |
| :--- | :--- | :--- |
| `pages/relawan/[id].vue` | `/relawan/701` | `id` bernilai `701` |
| `pages/relawan/[id].vue` | `/relawan/1871052` | `id` bernilai `1871052` |
| `pages/event/[kategori]/[slug].vue` | `/event/donor-darah/mall-lampung` | `kategori` = `donor-darah`, `slug` = `mall-lampung` |

---

## 2. Menangkap Parameter Menggunakan `useRoute()`
Di dalam `<script setup>` file halaman dinamis, kita memanggil fungsi helper bawaan Nuxt **`useRoute()`** untuk mengakses data parameter yang sedang aktif di URL browser saat ini:

```javascript
import { useRoute } from 'vue-router';
const route = useRoute();

// Mengakses parameter [id] sesuai nama file berkasnya
console.log(route.params.id);
```

---

## 3. Studi Kasus PMI: Halaman Detail Profil Relawan Dinamis

Mari kita buat file halaman detail relawan dinamis yang membaca ID dari URL dan menampilkan data profil yang sesuai.

### Langkah 1: Buat Berkas `pages/relawan/[id].vue`
Buat folder `pages/relawan/` jika belum ada, lalu ciptakan file bernama **`[id].vue`** dan isi dengan kode berikut:

```html
<!-- pages/relawan/[id].vue -->
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-6">
    <div class="flex justify-between items-center border-b pb-4">
      <h2 class="font-bold text-slate-800">Detail Anggota Relawan</h2>
      <span class="text-xs bg-slate-100 text-slate-500 px-3 py-1 rounded-full font-mono">
        ID: {{ idRelawan }}
      </span>
    </div>

    <!-- Tampilan Data Hasil Pencarian API -->
    <div v-if="dataRelawan" class="space-y-3 text-sm">
      <div class="flex justify-between">
        <span class="text-slate-400">Nama Lengkap:</span>
        <strong class="text-slate-800">{{ dataRelawan.nama }}</strong>
      </div>
      <div class="flex justify-between">
        <span class="text-slate-400">Golongan Darah:</span>
        <span class="px-2 py-0.5 bg-red-500 text-white text-xs font-bold rounded-md">
          {{ dataRelawan.gol }}
        </span>
      </div>
      <div class="flex justify-between">
        <span class="text-slate-400">Status Keanggotaan:</span>
        <span class="text-emerald-600 font-bold">✓ {{ dataRelawan.status }}</span>
      </div>
    </div>

    <!-- Halaman Error jika ID tidak ditemukan -->
    <div v-else class="text-center py-6 text-red-500 font-medium">
      🚨 Data relawan dengan ID tersebut tidak terdaftar di UDD.
    </div>

    <NuxtLink to="/relawan" class="block text-center text-xs text-slate-400 hover:underline">
      &larr; Kembali ke Daftar Relawan
    </NuxtLink>
  </div>
</template>

<script setup>
import { useRoute } from 'vue-router';
import { ref, onMounted } from 'vue';

const route = useRoute();
// 1. Ambil ID dari URL params sesuai nama berkas "[id].vue"
const idRelawan = route.params.id;

const dataRelawan = ref(null);

// Database simulasi relawan lokal
const databaseRelawan = [
  { id: "701", nama: "Budi Santoso", gol: "O", status: "Aktif" },
  { id: "702", nama: "Siti Rahma", gol: "A", status: "Aktif" },
  { id: "703", nama: "Ahmad Dahlan", gol: "AB", status: "Masa Tenggang" }
];

// 2. Cari data yang cocok saat halaman dimuat
onMounted(() => {
  const hasilCari = databaseRelawan.find(item => item.id === idRelawan);
  if (hasilCari) {
    dataRelawan.value = hasilCari;
  }
});
</script>
```

Buka alamat peramban Anda di **`http://localhost:3000/relawan/701`** atau **`http://localhost:3000/relawan/702`**. Anda akan melihat isi halaman berganti profil secara dinamis menyesuaikan data ID yang Anda ketikkan di URL browser!

---

## 4. Latihan Soal Mandiri
1. Amati kode pengambilan parameter: `const idRelawan = route.params.id;`.
2. Jika kita merubah nama berkas halaman dinamis kita dari `[id].vue` menjadi `[nikKtp].vue`, tuliskan bagaimana penulisan baris kode di JavaScript setup untuk mengakses parameter baru tersebut.
3. Sebutkan kelebihan pemisahan folder dinamis seperti `pages/relawan/[id].vue` dibandingkan membuat satu file halaman raksasa yang menampung seluruh logika detail menggunakan conditional rendering switch-case di satu URL.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pengambilan Parameter Baru
*   Jika nama file diubah menjadi `[nikKtp].vue`, maka nama kunci di objek params URL ikut berubah menyesuaikan nama berkas tersebut.
*   **Baris Kode Baru**:
    ```javascript
    const route = useRoute();
    const nikRelawan = route.params.nikKtp; // Sesuai nama persis variabel kurung siku
    ```

### Jawaban 3: Keunggulan Dynamic Pages
*   **Struktur Bersih (Modular)**: Logika untuk halaman daftar relawan (`relawan/index.vue`) dan halaman detail profil (`relawan/[id].vue`) terpisah di file fisik yang berbeda. Kode tidak menumpuk menjadi raksasa yang membingungkan untuk dipelihara.
*   **Hemat Resource Browser**: Browser hanya mengunduh skrip detail profil jika user benar-benar mengklik salah satu relawan.
*   **SEO & Deep Linking**: Tiap relawan memiliki URL uniknya sendiri yang bisa disimpan di bookmark browser atau dibagikan lewat chat whatsapp dinas pimpinan PMI untuk koordinasi cepat.
