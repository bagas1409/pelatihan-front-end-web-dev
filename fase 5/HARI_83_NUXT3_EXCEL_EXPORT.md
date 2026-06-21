# Hari 83: Fitur Ekspor Data ke Excel (Spreadsheet)
## Membangun Tombol Download Unduhan Laporan Relawan Format Excel Menggunakan Library XLSX

Selamat datang di Hari 83! Hari ini kita akan mempelajari cara membangun fitur **Ekspor Data ke Excel** (`.xlsx`) di Nuxt 3.

Pada portal administrasi UDD PMI, petugas admin seringkali butuh menyerahkan laporan data relawan aktif kepada dinas kesehatan provinsi. Dibbandingkan menyalin teks tabel web secara manual, kita akan merancang tombol pintar **"Unduh Laporan Excel"** yang bertugas menyusun data array JSON dari database menjadi lembaran sheet spreadsheet asli yang siap dibuka di Microsoft Excel.

---

## 1. Mengenal Library XLSX (SheetJS)

Kita akan menggunakan library manipulasi spreadsheet standar industri: **`xlsx`** (atau dikenal dengan **SheetJS**).
*   Library ini mampu mengonversi array objek JSON JavaScript:
    ```javascript
    [{ nama: "Budi", gol: "O" }]
    ```
*   Menjadi biner sheet Excel, membungkusnya ke dalam wadah Workbook, lalu mengunduhnya secara lokal lewat browser client.

---

## 2. Langkah Instalasi Library XLSX

Ketik perintah instalasi di terminal root proyek Nuxt Anda:
```bash
npm install xlsx
```

---

## 3. Studi Kasus PMI: Membuat Tombol Ekspor Excel Daftar Relawan

Mari kita buat halaman berisikan tombol unduh Excel di berkas **`pages/relawan/ekspor.vue`**.

```html
<!-- pages/relawan/ekspor.vue -->
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border mt-8 space-y-6">
    <div>
      <h3 class="font-bold text-slate-800 text-base">📊 Ekspor Laporan Relawan</h3>
      <p class="text-xs text-slate-400 mt-1">Unduh berkas Excel daftar anggota pendonor UDD</p>
    </div>

    <!-- Tampilan Data Ringkas -->
    <div class="bg-slate-50 p-4 rounded-2xl border border-slate-100 text-xs space-y-2">
      <div class="flex justify-between font-medium">
        <span>Total Data Siap Unduh:</span>
        <strong class="text-slate-800">{{ daftarRelawan.length }} Anggota</strong>
      </div>
      <div class="flex justify-between font-medium">
        <span>Format File Berkas:</span>
        <strong class="text-emerald-600">Microsoft Excel (.xlsx)</strong>
      </div>
    </div>

    <!-- Tombol Ekspor -->
    <button 
      @click="unduhExcel" 
      :disabled="loading"
      class="w-full bg-emerald-500 hover:bg-emerald-600 disabled:bg-slate-300 text-white font-bold py-3 rounded-xl text-xs transition-colors flex items-center justify-center gap-2"
    >
      <span>{{ loading ? '⏳ Menyusun Sheet...' : '📥 Unduh Laporan Excel' }}</span>
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

const loading = ref(false);
const { $toast } = useNuxtApp();

// Data database simulasi relawan dari API
const daftarRelawan = ref([
  { NO: 1, NAMA: 'Ahmad Syarif', GOLONGAN: 'O', WILAYAH: 'Bandar Lampung', STATUS: 'Aktif' },
  { NO: 2, NAMA: 'Budi Santoso', GOLONGAN: 'A', WILAYAH: 'Metro', STATUS: 'Aktif' },
  { NO: 3, NAMA: 'Siti Rahma', GOLONGAN: 'AB', WILAYAH: 'Lampung Selatan', STATUS: 'Aktif' }
]);

const unduhExcel = async () => {
  loading.value = true;

  try {
    // 1. Impor dinamis library XLSX hanya saat tombol di-klik di client browser
    const XLSX = await import('xlsx');

    // 2. Buat objek lembar kerja (Worksheet) baru dari array data JSON kita
    const worksheet = XLSX.utils.json_to_sheet(daftarRelawan.value);

    // 3. Buat buku kerja (Workbook) pembungkus kosong
    const workbook = XLSX.utils.book_new();

    // 4. Masukkan worksheet ke dalam workbook, beri nama sheet "Daftar Relawan"
    XLSX.utils.book_append_sheet(workbook, worksheet, 'Daftar Relawan');

    // 5. Perintahkan browser mengunduh berkas fisik Excel tersebut ke komputer user
    // Parameter kedua adalah nama berkas file saat tersimpan di komputer
    XLSX.writeFile(workbook, 'Laporan_Relawan_PMI_Lampung.xlsx');

    $toast.success("✓ Laporan Excel berhasil diunduh!");

  } catch (err) {
    console.error("Gagal ekspor excel:", err);
    $toast.error("🚨 Gagal menyusun file Excel!");
  } finally {
    loading.value = false;
  }
};
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati kode pembuatan sheet: `XLSX.utils.json_to_sheet(daftarRelawan.value)`.
2. Jelaskan bagaimana library XLSX menentukan nama-nama kolom header (baris paling atas) pada file Excel hasil unduhan tersebut berdasarkan format objek JSON inputan kita.
3. Sebutkan kelebihan dari pemanggilan impor dinamis `const XLSX = await import('xlsx')` di dalam fungsi klik tombol daripada menaruhnya di baris paling atas halaman menggunakan tag `import XLSX from 'xlsx'`.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Penentuan Header Kolom Excel
*   **Deteksi Kunci Objek (Object Keys)**: Library XLSX membaca nama properti kunci (*Keys*) dari objek pertama di dalam array JSON secara otomatis.
*   **Hasil Penentuan**: Karena objek kita didefinisikan dengan huruf kapital: `{ NAMA: '...', GOLONGAN: '...' }`, maka baris paling atas (Header Row) pada sheet Excel secara otomatis akan bertuliskan kolom **NAMA, GOLONGAN, WILAYAH,** dan **STATUS** secara rapi tanpa perlu konfigurasi layout manual.

### Jawaban 3: Keunggulan Dynamic Import (Code Splitting)
*   **Menghemat Bandwidth Awal**: Library XLSX/SheetJS adalah library manipulasi biner yang ukurannya tergolong sangat besar (sekitar 300 Kilobyte).
*   Jika kita mengimpornya di baris paling atas file secara standar, browser petugas **wajib mengunduh file 300KB tersebut setiap kali halaman dimuat**, meskipun petugas tidak berniat mendownload Excel saat itu. 
*   Dengan impor dinamis `await import()`, file XLSX hanya akan diunduh dari server cloud **pada milidetik saat tombol diklik pertama kali**. Halaman awal dimuat dengan sangat ringan dan cepat, menghemat pemakaian RAM laptop petugas admin.
