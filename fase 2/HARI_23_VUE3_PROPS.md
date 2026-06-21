# Hari 23: Komponen Reusable - Bekerja dengan Props
## Mengirim Data dari Komponen Induk Ke Komponen Anak Menggunakan defineProps

Selamat datang di Hari 23! Hari ini kita akan mempelajari cara merancang komponen yang mandiri dan fleksibel (*Reusable*) dengan mengalirkan data satu arah dari komponen Induk (*Parent*) ke komponen Anak (*Child*) menggunakan fungsi makro **`defineProps`** di Vue 3.

Saat mendesain halaman dashboard admin DonorKu, kita akan menampilkan banyak kartu metrik ringkasan (seperti kartu total relawan, kartu stok kritis, dll). Daripada menulis kode HTML kartu yang sama berulang-ulang sebanyak 4 kali dengan menyalin-nempel kode, kita cukup membuat satu cetakan kartu ajaib lalu mengirimkan data judul, nilai, dan warna yang berbeda menggunakan **Props**.

---

## 1. Aliran Data Satu Arah (One-Way Data Flow)

### 💡 Analogi Dunia Nyata: "Mesin Cetakan Cap Stempel Surat UDD"
*   **Komponen Anak (Gagang Stempel Karet)**: Gagang stempel karet dirancang memiliki pola kotak kosong yang sama di bawahnya. Stempel karet tidak memiliki warna tinta bawaan.
*   **Komponen Induk (Petugas yang Memegang Bak Tinta Warna)**: Petugas UDD menempelkan gagang stempel ke bak tinta merah (mengirim prop warna `"merah"`) dan menekannya ke kertas bertuliskan `"Darurat CITO"` (mengirim prop teks). Di kertas berikutnya, petugas menggunakan bak tinta hijau dan tulisan `"Aman"`.
*   Stempel karet (komponen anak) murni hanya menggambar apa yang dipasok oleh petugas (komponen induk). Anak dilarang merubah warna tinta di bak induk secara mandiri (*One-Way Data Flow*).

---

## 2. Mendefinisikan Props dengan `defineProps`

Di dalam script setup Vue 3, kita mendefinisikan properti apa saja yang siap diterima dari luar menggunakan fungsi makro **`defineProps`**. Kita disarankan memberikan validasi tipe data agar kode lebih aman dari error salah tipe data.

```html
<!-- components/CardMetrik.vue (Komponen Anak) -->
<template>
  <div 
    class="p-6 rounded-2xl border shadow-sm transition-all hover:shadow-md"
    :class="isAlert ? 'bg-red-50 border-red-100' : 'bg-white border-slate-200'"
  >
    <h4 class="text-xs font-bold uppercase tracking-wider" :class="isAlert ? 'text-red-500' : 'text-slate-400'">
      {{ label }}
    </h4>
    <div class="flex justify-between items-baseline mt-4">
      <p class="text-3xl font-black text-slate-800">{{ nilai }}</p>
      <span class="text-xs font-semibold" :class="isAlert ? 'text-red-600' : 'text-slate-500'">
        {{ satuan }}
      </span>
    </div>
  </div>
</template>

<script setup>
// defineProps tidak perlu di-impor, dia adalah fungsi makro bawaan Vue 3
defineProps({
  label: {
    type: String,
    required: true // Wajib diisi dari luar
  },
  nilai: {
    type: [Number, String], // Boleh berupa Angka atau Teks
    default: 0
  },
  satuan: {
    type: String,
    default: 'Kantong'
  },
  isAlert: {
    type: Boolean,
    default: false // Jika tidak diisi, otomatis bernilai false
  }
});
</script>
```

---

## 3. Memanggil Komponen Anak di Komponen Induk

Setelah komponen anak di atas disimpan di folder `components/CardMetrik.vue`, Nuxt 3 akan mendeteksi dan mengimpornya secara otomatis. Kita tinggal memanggilnya di halaman utama `pages/index.vue` dengan menyuplai data props-nya.

```html
<!-- pages/index.vue (Komponen Induk) -->
<template>
  <div class="p-8 bg-slate-50 min-h-screen">
    <h2 class="text-xl font-bold mb-6">Monitoring Logistik UDD Lampung</h2>
    
    <!-- GRID LAYOUT -->
    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
      
      <!-- Memanggil Komponen Anak dengan suplai Props berbeda-beda -->
      <CardMetrik 
        label="Total Relawan Terdaftar" 
        :nilai="1240" 
        satuan="Orang" 
      />

      <CardMetrik 
        label="Persediaan Darah O" 
        :nilai="stokGolO" 
        satuan="Bag" 
      />

      <!-- :is-alert="true" menggunakan tanda titik dua (:) agar nilainya dibaca sebagai boolean asli, bukan string -->
      <CardMetrik 
        label="Darurat CITO Aktif" 
        :nilai="2" 
        satuan="Kasus" 
        :is-alert="true" 
      />

    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const stokGolO = ref(98);
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati kode pemanggilan komponen anak di atas: `<CardMetrik label="Persediaan Darah O" :nilai="stokGolO" />`.
2. Jelaskan perbedaan fungsi penulisan atribut **tanpa tanda titik dua** (`label="..."`) dengan atribut **yang menggunakan tanda titik dua** (`:nilai="..."`) di Vue 3.
3. Apa yang akan dialami oleh browser jika komponen induk mengirimkan data bertipe string: `:nilai="'Seratus'"` ke komponen anak, sedangkan di `defineProps` kolom nilai diatur hanya menerima tipe `Number` (`type: Number`) tanpa alternatif tipe string?

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Aturan Binding Properti (:)
*   **Tanpa Titik Dua (Static Attribute)**: Mengalirkan data sebagai **String Polos**. Nilai yang ditangkap anak adalah teks string mentah huruf per huruf.
*   **Dengan Titik Dua (Dynamic Binding / `v-bind`)**: Menginstruksikan Vue untuk mengevaluasi teks di dalam tanda kutip sebagai **Ekspresi JavaScript**. Properti `:nilai="stokGolO"` mengalirkan isi data variabel reaktif `stokGolO` (yaitu angka `98`), bukan teks string `"stokGolO"`. Ini juga wajib digunakan untuk tipe data boolean, angka, array, atau objek.

### Jawaban 3: Peringatan Validasi Props
*   Aplikasi website akan tetap berjalan dan merender teks *"Seratus"* di layar. Namun, di jendela konsol pengembang (F12 Console Log) browser, Vue 3 akan memunculkan peringatan error berwarna kuning terang (**Warning Alert**): 
    `[Vue warn]: Invalid prop: type check failed for prop "nilai". Expected Number, got String with value "Seratus".`
*   **Alasan**: Validasi tipe data di `defineProps` bertindak sebagai polisi penunjuk arah kualitas kode during development. Dia mendeteksi adanya ketidaksesuaian kontrak data antara tim yang membuat komponen anak dengan tim yang menyuplai data di komponen induk, mempermudah pelacakan salah tipe data sebelum aplikasi dirilis ke server produksi.
