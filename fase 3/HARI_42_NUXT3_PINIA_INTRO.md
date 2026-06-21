# Hari 42: Pengenalan Pinia State Management
## Memahami Filosofi State Management Terpusat Dibandingkan Melemparkan Data Props Berulang-ulang

Selamat datang di Hari 42! Hari ini kita akan mempelajari konsep **State Management Terpusat** (Centralized State Management) menggunakan pustaka resmi Vue 3: **Pinia** (pengganti resmi Vuex).

Saat aplikasi web kita bertumbuh besar memiliki belasan halaman dan sub-komponen bersarang, kita akan menghadapi kesulitan membagikan data global (seperti nama akun petugas yang sedang login atau token akses API). Mengalirkan data tersebut menggunakan Props ke bawah dan Emits ke atas berulang kali melewati belasan tingkat komponen (*Props Drilling*) sangat melelahkan dan mudah pecah. Pinia lahir sebagai database internal pusat di sisi client yang bisa diakses langsung oleh komponen manapun secara instan.

---

## 1. Masalah Props Drilling vs Solusi Pinia

### 💡 Analogi Dunia Nyata: "Papan Pengumuman Sentral vs Sistem Operan Estafet Kertas"
Bayangkan sebuah kantor cabang UDD PMI Lampung memiliki 4 lantai gedung. Pimpinan UDD di Lantai 4 ingin mengabarkan info *"Stok Darah O Kosong"* ke petugas lapangan di Lantai 1.
*   **Props Drilling (Estafet Kertas - Melelahkan)**: Pimpinan menulis kertas, menyerahkan ke staf Lantai 3. Staf lantai 3 berjalan menyerahkan ke staf Lantai 2. Staf lantai 2 berjalan menyerahkan ke staf Lantai 1. 
    *   Jika salah satu staf di lantai tengah keluar kantor/sakit, kertas pengumuman terhenti pecah di tengah jalan. Dan jika staf lantai 1 ingin membalas pesan, dia harus melakukan estafet kertas arah sebaliknya (*Emits Drilling*).
*   **Pinia (Papan Pengumuman Sentral / Speaker Lorong)**: Kantor memasang **Sistem Speaker Pengeras Suara Sentral** di lorong gedung. 
    *   Pimpinan di Lantai 4 cukup berbicara lewat mikrofon sekali, dan seluruh staf di Lantai 1, 2, dan 3 langsung mendengar info tersebut secara bersamaan dari speaker kamar masing-masing tanpa perlu ada aksi operan estafet berjalan kaki.

---

## 2. Tiga Komponen Utama di Store Pinia
Di Pinia, sebuah gudang data terpusat (Store) memiliki 3 bagian yang berpasangan persis dengan konsep komponen Vue biasa:
1.  **State (setara `data`)**: Penampung variabel data utama yang sensitif reaktif (seperti `const token = ref(null)`).
2.  **Getters (setara `computed`)**: Properti terhitung yang menyaring state (seperti menghitung status apakah user sudah terautentikasi atau belum).
3.  **Actions (setara `methods`)**: Kumpulan fungsi JavaScript untuk memanipulasi state atau memanggil API server (seperti fungsi login, logout, atau fetch data).

---

## 3. Langkah Instalasi Pinia di Nuxt 3

### Langkah 1: Instal Dependensi
Ketik perintah berikut di terminal root proyek Nuxt 3 Anda:
```bash
npm install pinia @pinia/nuxt
```

### Langkah 2: Daftarkan Modul di `nuxt.config.ts`
Buka file konfigurasi Nuxt dan daftarkan modul `@pinia/nuxt` ke dalam array modules:

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  compatibilityDate: '2024-04-03',
  devtools: { enabled: true },
  modules: [
    '@nuxtjs/tailwindcss',
    '@pinia/nuxt' // Daftarkan modul Pinia di sini!
  ]
})
```

Setelah didaftarkan, modul Pinia siap di-import secara otomatis oleh Nuxt. Kita bisa langsung membuat folder **`stores/`** untuk menampung file-file gudang data kita.

---

## 4. Latihan Soal Mandiri
1. Amati perbedaan arsitektur Props Drilling dengan State Management Terpusat.
2. Jelaskan mengapa kita tidak disarankan menggunakan Pinia untuk menyimpan data yang sifatnya sangat lokal sekali (seperti status terbuka/tertutupnya sebuah tombol modal pop-up kecil di satu halaman).
3. Sebutkan kelebihan Pinia dibandingkan pendahulunya, **Vuex**, dalam hal penulisan sintaks di Vue 3.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kapan Menggunakan Pinia
*   **Mengapa dilarang untuk data lokal**: Menyimpan state lokal (seperti toggle modal `isOpen`) ke dalam Pinia akan merusak prinsip enkapsulasi komponen (*Component Isolation*). Jika ada 3 halaman berbeda memanggil komponen modal yang sama dan semuanya terikat ke 1 state `isOpen` di Pinia, menekan tombol di halaman A akan membuat modal di halaman B dan C ikut terbuka secara tidak sengaja.
*   **Aturan Emas**: Gunakan Pinia hanya untuk **Data Global** yang dibutuhkan oleh banyak halaman berbeda (seperti: sesi akun login, tema dark/light mode, keranjang belanjaan, notifikasi CITO). Gunakan `ref()` lokal untuk data yang hanya mempengaruhi 1 komponen itu saja.

### Jawaban 3: Keunggulan Pinia vs Vuex
1.  **Tanpa Mutations**: Di Vuex, kita terpaksa menulis kode panjang memisahkan *Actions* dan *Mutations* hanya untuk mengubah sebuah variabel. Pinia membuang Mutations secara total; kita bebas merubah state langsung di dalam Actions layaknya memanipulasi variabel `.value` biasa.
2.  **Dukungan TypeScript Penuh**: Pinia dirancang menggunakan TypeScript sejak awal, memberikan fitur auto-complete pengetikan yang sangat akurat di VS Code.
3.  **Gaya Penulisan composition API**: Pinia mendukung gaya penulisan fungsi `defineStore` menggunakan setup syntax yang identik dengan gaya menulis `<script setup>` di Vue 3.
