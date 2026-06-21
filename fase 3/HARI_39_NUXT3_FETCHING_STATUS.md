# Hari 39: Status Fetching & Error Handling
## Menampilkan Animasi Loading Skeleton & Pesan Koneksi Padam

Selamat datang di Hari 39! Hari ini kita akan mempelajari cara merancang antarmuka penanganan status penarikan data yang elegan di Nuxt 3 menggunakan parameter **`pending`**, **`error`**, dan fungsi penyegaran data **`refresh`**.

Aplikasi web kelas dunia tidak pernah membiarkan layar kosong melompong secara misterius saat mengambil data dari internet yang lambat. Kita wajib memberikan umpan balik visual yang interaktif kepada petugas dinas UDD PMI Lampung: apakah data sedang dimuat (Loading), terjadi kegagalan server (Error), atau koneksi internet terputus.

---

## 1. Tiga Pilar Status Fetching di Nuxt 3

Setiap kali kita memanggil API menggunakan `useFetch`, Nuxt mengembalikan tiga variabel status reaktif yang siap kita gunakan di area template HTML:

1.  **`pending` (Boolean)**: Bernilai `true` selama data sedang berjalan di kabel internet. Sangat pas untuk menampilkan roda berputar (*Spinner*) atau kerangka memuat (*Skeleton Loader*).
2.  **`error` (Object)**: Berisi detail kegagalan jika server mati (seperti kode error HTTP `500` atau koneksi putus).
3.  **`refresh` (Function)**: Tombol pemicu untuk menembak ulang API yang gagal tanpa menyegarkan seluruh halaman.

---

## 2. Studi Kasus PMI: Merancang Layar Loading & Saringan Error UDD

Mari kita buat halaman kelola data stok darah yang memiliki penanganan status loading dan error yang premium dan lengkap.

```html
<template>
  <div class="max-w-md mx-auto p-6 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-4">
    
    <div class="flex justify-between items-center border-b pb-3">
      <h3 class="font-bold text-slate-800">🩸 Persediaan Darah UDD</h3>
      <!-- Tampilkan lampu indikator loading kecil jika sedang refresh di latar belakang -->
      <span v-if="pending && daftarStok" class="text-[10px] text-amber-500 animate-pulse font-bold">
        🔄 Sinkronisasi...
      </span>
    </div>

    <!-- 1. STATUS ERROR (SERVER MATI / INTERNET PUTUS) -->
    <div v-if="error" class="p-5 bg-red-50 border border-red-200 rounded-2xl text-center space-y-3">
      <p class="text-xs text-red-800 font-medium">
        🚨 Gagal terhubung ke database server PMI: <strong>{{ error.message || 'Koneksi Terputus' }}</strong>
      </p>
      <!-- Tombol coba lagi menggunakan fungsi refresh -->
      <button 
        @click="refresh" 
        class="px-4 py-2 bg-red-500 hover:bg-red-600 text-white font-bold text-xs rounded-xl shadow-sm transition-colors active:scale-95"
      >
        Coba Hubungkan Kembali
      </button>
    </div>

    <!-- 2. STATUS LOADING (SKELETON LOADER) -->
    <!-- Aktif hanya jika data awal benar-benar kosong dan status pending = true -->
    <div v-else-if="pending && !daftarStok" class="space-y-3">
      <div v-for="i in 3" :key="i" class="animate-pulse flex justify-between items-center py-2">
        <!-- Kotak abu-abu berkedip menyerupai baris tabel asli -->
        <div class="h-4 bg-slate-200 rounded w-24"></div>
        <div class="h-4 bg-slate-200 rounded w-12"></div>
      </div>
    </div>

    <!-- 3. STATUS SUKSES (DATA DIRENDER) -->
    <ul v-else class="divide-y divide-slate-100 text-sm">
      <li v-for="item in daftarStok" :key="item.id" class="py-3 flex justify-between items-center">
        <span class="text-slate-600 font-medium">Golongan {{ item.golongan }}</span>
        <span class="font-bold text-slate-900">{{ item.jumlah }} Kantong</span>
      </li>
    </ul>

  </div>
</template>

<script setup>
// Amati penulisan lazy: true agar halaman langsung terbuka tanpa memblokir navigasi
const { data: daftarStok, pending, error, refresh } = await useFetch('https://pmi-backend-api.onrender.com/api/stocks', {
  lazy: true,
  pick: ['id', 'golongan', 'jumlah'] // Optimasi: Hanya mengambil properti penting saja dari API
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati opsi `{ lazy: true }` pada fungsi `useFetch` di atas.
2. Jelaskan perbedaan kenyamanan visual yang dirasakan petugas admin jika kita menyalakan opsi `lazy: true` dibandingkan dengan default-nya (`lazy: false`).
3. Jelaskan apa kegunaan opsi optimasi **`pick`** pada pemanggilan API di atas dan hubungannya dengan penghematan RAM komputer.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Manfaat Opsi Lazy
*   **Default (`lazy: false`)**: Saat petugas mengklik menu stok, browser **menahan perpindahan rute selama 2 detik** (layar monitor mematung di halaman lama). Halaman stok baru akan terbuka setelah data API selesai diunduh. Pengguna mengira website macet karena navigasi tidak segera merespon klik.
*   **Lazy (`lazy: true`)**: Halaman stok **langsung terbuka dalam 1 milidetik**. Struktur kartu kosong dan animasi berkedip (*Skeleton Loader*) langsung muncul seketika memberi umpan balik visual bahwa halaman sedang memuat konten. Ini meningkatkan kenyamanan visual pengguna secara dramatis (*Visual Responsiveness*).

### Jawaban 3: Kegunaan Opsi Pick
*   **Fungsi `pick`**: Membatasi properti data apa saja yang disalin masuk ke dalam variabel reaktif Vue. Jika API mengembalikan 20 kolom data tebal (termasuk tanggal dibuat, data log audit, detail relawan donor), namun kita hanya memilih `['id', 'golongan', 'jumlah']`, Nuxt akan membuang 17 kolom sisanya secara otomatis.
*   **Hubungan dengan RAM**: Mengurangi ukuran memori objek Proxy reaktif di RAM browser client, mempercepat proses rendering baris tabel, dan meringankan beban CPU handphone dinas petugas saat memproses ribuan data.
