# Hari 21: Kondisional & List Rendering
## Mengontrol Tampilan Menu Dinamis & Merender Tabel Riwayat Donor Relawan

Selamat datang di Hari 21! Hari ini kita akan mempelajari cara menampilkan komponen secara fleksibel menggunakan direktif kondisional (**`v-if`**, **`v-else`**) dan cara menampilkan deretan data berulang menggunakan direktif list rendering (**`v-for`**).

Di portal DonorKu, kita butuh menampilkan pesan alarm darurat hanya jika status stok benar-benar kritis, dan kita butuh menampilkan tabel berisi puluhan baris data relawan secara otomatis dari array database. Kita akan merakit fitur-fitur visual ini.

---

## 1. Direktif Kondisional: v-if vs v-show

Vue menyediakan dua cara untuk menyembunyikan/menampilkan elemen di layar:
*   **`v-if` (Bongkar Pasang Fisik)**: Jika kondisi salah (false), Vue akan **menghapus secara total** elemen HTML tersebut dari dokumen DOM. Jika kondisi benar (true), Vue baru merakit kembali elemen tersebut. Ini sangat hemat memori RAM jika elemen tersebut jarang dibuka (misal: modal rahasia).
*   **`v-show` (Tutup Tirai CSS)**: Elemen tetap terpasang kokoh di HTML, namun Vue hanya mengganti gaya CSS-nya menjadi `display: none` secara instan. Sangat cocok untuk elemen yang sering berkedip mati-menyala berulang kali dalam hitungan detik.

---

## 2. List Rendering: v-for & Mengapa Atribut `:key` Wajib Ditulis

Saat melakukan perulangan array menggunakan `v-for`, kita wajib menyertakan atribut **`key`** yang diisi dengan nilai pengenal unik (seperti ID unik dari database):

```html
<tr v-for="relawan in daftarRelawan" :key="relawan.id">
  <td>{{ relawan.nama }}</td>
</tr>
```

### 💡 Analogi Dunia Nyata: "Gantungan Kunci Loker Tas Relawan"
Bayangkan ada 5 relawan menitipkan tas di loker UDD PMI. Anda menempelkan gantungan nomor kunci unik (`:key`) pada tiap laci loker.
*   Jika salah satu relawan mengambil tasnya di laci tengah, Anda dengan mudah mengambil tas tersebut tanpa perlu meraba dan mengacak-acak isi tas di laci 1, 2, dan 4.
*   Tanpa kunci pengenal unik (`:key`), jika ada baris data di tengah tabel yang dihapus atau digeser posisinya, browser Vue terpaksa membongkar dan menggambar ulang seluruh baris tabel dari atas ke bawah karena kehilangan jejak koordinat baris data tersebut. Atribut `:key` menjamin performa rendering yang sangat cepat.

---

## 3. Studi Kasus PMI: Halaman Pengumuman & Tabel Antrean Donor

Mari kita buat komponen gabungan yang menampilkan peringatan status kritis dan merender daftar relawan yang sedang mengantre donor di UDD.

```html
<template>
  <div class="max-w-xl mx-auto p-6 bg-slate-50 min-h-screen">
    
    <!-- 1. ALERT KONDISIONAL (v-if) -->
    <!-- Hanya muncul jika status darurat bernilai true -->
    <div 
      v-if="isDarurat" 
      class="p-4 mb-6 bg-red-100 border border-red-200 text-red-800 rounded-2xl animate-pulse flex items-center justify-between"
    >
      <div class="flex items-center gap-2">
        <span>🚨</span>
        <span class="font-bold text-xs">PANGGILAN CITO: Stok Darah O menipis di UDD Metro!</span>
      </div>
      <button @click="isDarurat = false" class="text-xs font-bold hover:underline">Tutup</button>
    </div>

    <!-- 2. DAFTAR ANTREAN (v-for) -->
    <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm">
      <div class="flex justify-between items-center mb-4">
        <h3 class="font-bold text-slate-800">Antrean Donor Hari Ini</h3>
        <span class="text-xs bg-slate-100 px-3 py-1 rounded-full font-semibold text-slate-500">
          {{ antrean.length }} Orang Antre
        </span>
      </div>

      <!-- Jika ada antrean, tampilkan list -->
      <ul v-if="antrean.length > 0" class="divide-y divide-slate-100">
        <li 
          v-for="(orang, index) in antrean" 
          :key="orang.id" 
          class="py-3 flex justify-between items-center"
        >
          <div>
            <span class="text-xs text-slate-400 font-bold mr-2">#{{ index + 1 }}</span>
            <span class="font-semibold text-slate-700">{{ orang.nama }}</span>
          </div>
          <span 
            class="text-[10px] font-bold px-3 py-1 rounded-full uppercase"
            :class="orang.gol === 'O' ? 'bg-red-500 text-white' : 'bg-slate-100 text-slate-600'"
          >
            Gol {{ orang.gol }}
          </span>
        </li>
      </ul>

      <!-- Jika antrean kosong, tampilkan pesan ramah (v-else) -->
      <div v-else class="text-center py-8 text-slate-400 text-sm">
        🕊️ Belum ada antrean donor aktif saat ini.
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue';

const isDarurat = ref(true);

// Data array list antrean donor
const antrean = ref([
  { id: 701, nama: "Budi Santoso", gol: "O" },
  { id: 702, nama: "Siti Rahma", gol: "A" },
  { id: 703, nama: "Ahmad Dahlan", gol: "O" },
  { id: 704, nama: "Dewi Lestari", gol: "AB" }
]);
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati kode perulangan `v-for="(orang, index) in antrean" :key="orang.id"` di atas.
2. Mengapa kita tidak disarankan mengisi nilai `:key` dengan variabel `index` bawaan loop (seperti `:key="index"`) jika isi array tersebut bersifat dinamis (dapat ditambah, dihapus, atau diurutkan ulang)? Jelaskan dampaknya.
3. Sebutkan perbedaan perilaku teknis di browser antara direktif `v-if` dan `v-show`.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Bahaya Menggunakan Index sebagai Key
*   Menggunakan `index` loop sebagai `:key` sangat dilarang untuk array dinamis.
*   **Alasan**: Nilai index selalu berurutan `0, 1, 2, 3...` berapapun isi datanya. Jika Anda menghapus baris nomor 2, baris nomor 3 otomatis bergeser naik mengambil alih posisi index `2`. 
*   **Dampaknya**: Vue mengira elemen baris nomor 2 tidak pernah dihapus (karena index `2` tetap ada), sehingga Vue akan salah merender isi komponen visual tersebut, memicu terjadinya bug visual di layar (seperti data checkbox yang tertinggal salah baris) dan menurunkan performa rendering aplikasi. Gunakan selalu ID fisik asli dari database yang tidak pernah berubah.

### Jawaban 3: Perbedaan Teknis v-if vs v-show
1.  **`v-if` (Conditional Creation)**: Bersifat malas (*lazy*). Jika kondisi awal adalah false, dia tidak akan menulis kode HTML tersebut ke halaman browser sama sekali. Memiliki beban awal (*Initialization Cost*) yang ringan, namun beban perpindahan (*Toggle Cost*) yang berat karena harus merakit dan menghancurkan elemen DOM fisik berulang kali.
2.  **`v-show` (Conditional Visibility)**: Selalu merender dan menulis kode HTML ke halaman browser sejak awal, terlepas dari apa status kondisinya. Hanya memanipulasi properti CSS `display: none / block` sehingga memiliki beban awal yang sedikit lebih berat, namun proses perpindahan (*Toggle*) terasa sangat ringan dan instan karena browser tidak perlu membangun struktur DOM baru.
