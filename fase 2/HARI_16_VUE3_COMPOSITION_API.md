# Hari 16: Pengenalan Vue 3 - Composition API vs Options API
## Memahami Cara Mengatur Struktur Logika Kode Menggunakan Setup Script Modern

Selamat datang di Hari 16! Kita memasuki Fase 2. Di fase ini kita akan mempelajari framework Vue 3 & Nuxt 3. Hari ini kita akan fokus memahami dasar dari **Vue 3** dan membandingkan gaya penulisan kode **Composition API** (gaya baru) dengan **Options API** (gaya lama).

Vue adalah framework progresif yang sangat ramah pemula namun memiliki performa tinggi. Pada Vue versi 3, Meta memperkenalkan cara mengelompokkan logika kode yang disebut Composition API. Ini menggantikan gaya lama agar kode kita lebih modular, mudah dibaca, dan aman dari bug saat proyek membesar.

---

## 1. Perbedaan Mendasar: Options API vs Composition API

### 💡 Analogi Dunia Nyata: "Menata Lemari Baju Berdasarkan Jenis vs Berdasarkan Setelan Acara"
Bayangkan Anda ingin bepergian ke dua acara berbeda: Dinas PMI (Kemeja & Celana Bahan) dan Olahraga (Kaos & Celana Training).
*   **Options API (Menata Berdasarkan Jenis Baju - Tersebar)**: Di dalam lemari, Anda memisahkan baju secara kaku: Laci 1 khusus atasan, Laci 2 khusus bawahan, Laci 3 khusus sepatu.
    *   Saat ingin bersiap dinas PMI, Anda terpaksa membuka Laci 1 mengambil kemeja, membuka Laci 2 mengambil celana bahan, dan membuka Laci 3 mengambil sepatu pantofel. Anda harus naik turun laci terus-menerus. Di kode program, ini adalah pengelompokan berdasarkan opsi: `data()`, `methods: {}`, dan `mounted()`. Logika satu fitur terpecah-pecah di berbagai tempat.
*   **Composition API (Menata Berdasarkan Setelan Acara - Terkumpul)**: Anda menggantung kemeja, celana bahan, dan sepatu pantofel dinas PMI secara berdampingan di satu hanger gantungan baju yang sama. Di gantungan sebelah, Anda menggantung setelan olahraga.
    *   Saat bersiap dinas, Anda cukup mengambil satu hanger gantungan tersebut secara instan. Logika kode dikelompokkan secara rapat berdasarkan **fungsi fitur** itu sendiri, bukan berdasarkan tipe opsinya.

---

## 2. Perbandingan Struktur Kode

Mari kita lihat perbedaan saat kita membuat panel counter penghitung jumlah kantong darah masuk.

### Cara Options API (Gaya Lama):
Logika terpisah secara kaku di dalam blok opsi `data` dan `methods`.

```html
<template>
  <div>
    <p>Jumlah Donor: {{ jumlah }}</p>
    <button @click="tambahDonor">Tambah</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      jumlah: 0 // Data ditaruh di sini
    };
  },
  methods: {
    tambahDonor() {
      this.jumlah++; // Fungsi manipulasi ditaruh terpisah di sini
    }
  }
}
</script>
```

### Cara Composition API dengan `<script setup>` (Gaya Modern):
Logika dideklarasikan menyatu secara alami layaknya menulis JavaScript biasa.

```html
<template>
  <div>
    <!-- Variabel 'jumlah' langsung diikat di template -->
    <p>Jumlah Donor: {{ jumlah }}</p>
    <button @click="tambahDonor">Tambah</button>
  </div>
</template>

<!-- Tambahkan atribut setup di tag script -->
<script setup>
import { ref } from 'vue';

// Logika data & fungsi berkumpul bersebelahan secara harmonis!
const jumlah = ref(0);

const tambahDonor = () => {
  jumlah.value++; // Variabel bertipe ref diakses nilainya lewat properti ".value"
};
</script>
```

---

## 3. Mengapa `<script setup>` adalah Standar Emas Saat Ini?
Sintaks `<script setup>` adalah pembungkus khusus di Vue 3 yang memiliki banyak kelebihan:
1.  **Tanpa `export default`**: Tidak perlu lagi menulis struktur boilerplate obyek pembungkus yang tebal.
2.  **Impor Komponen & Variabel Otomatis Terekspos**: Semua variabel, fungsi, atau file komponen anak yang diimpor di dalam `<script setup>` secara otomatis langsung bisa dibaca di area `<template>` tanpa perlu repot didaftarkan satu per satu.
3.  **Bebas dari Masalah Referensi `this`**: Kita tidak akan pernah pusing lagi mencari tahu mengapa `this.jumlah` bernilai `undefined` karena kita murni menggunakan cakupan variabel JS biasa.

---

## 4. Latihan Soal Mandiri
1. Amati kode perbandingan di atas.
2. Tuliskan rincian mengapa pada Composition API kita harus mengakses data reaktif dengan menuliskan `jumlah.value++` di bagian `<script setup>`, sedangkan di bagian `<template>` kita cukup menuliskan `{{ jumlah }}` saja tanpa properti `.value`.
3. Tuliskan struktur Composition API `<script setup>` sederhana untuk membuat tombol "Toggle Status" yang merubah variabel boolean `isOnline` dari `true` menjadi `false` dan sebaliknya saat diklik.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Alasan Penggunaan Properti `.value`
*   **Di bagian `<script setup>`**: Variabel `jumlah` dibungkus di dalam objek reaktif bernama **Proxy**. Untuk mengubah atau membaca isi data asli di dalam bungkus proxy tersebut secara manual di JavaScript, kita harus memanggil properti dalamnya yaitu `.value`.
*   **Di bagian `<template>`**: Mesin kompilator Vue 3 secara cerdas mendeteksi bahwa variabel tersebut adalah objek reaktif, lalu secara otomatis melakukan proses **Unwrapping** (membongkar bungkus proxy secara otomatis). Pengembang tidak perlu menulis `.value` di HTML agar kode template tetap bersih dan mudah dibaca.

### Jawaban 3: Implementasi Toggle Status:
```html
<template>
  <div>
    <p>Status Koneksi: <strong :class="isOnline ? 'text-green-500' : 'text-red-500'">{{ isOnline ? 'Online' : 'Offline' }}</strong></p>
    <button @click="toggleStatus" class="bg-slate-800 text-white px-4 py-2 rounded-lg">
      Ubah Koneksi
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const isOnline = ref(true);

const toggleStatus = () => {
  isOnline.value = !isOnline.value; // Membalik nilai boolean
};
</script>
```
Dengan struktur setup ini, kode kita sangat pendek, efisien, dan bersih.
