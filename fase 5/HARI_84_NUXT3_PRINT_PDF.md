# Hari 84: Fitur Cetak Laporan PDF
## Menyusun Templat Cetak Laporan Medis UDD PMI Menggunakan Window.print() & CSS Page Break

Selamat datang di Hari 84! Hari ini kita akan mempelajari cara membangun fitur **Cetak Laporan PDF** (Print Report) di Nuxt 3.

Saat menyerahkan logistik fisik kepada kurir dinas atau rumah sakit, petugas UDD PMI membutuhkan lembaran **Surat Jalan / Laporan Logistik** fisik yang dicetak di atas kertas printer. Dibandingkan menginstal library PDF eksternal yang rumit dan berat di browser, kita akan menggunakan cara paling stabil dan efisien: memanfaatkan engine cetak printer bawaan browser menggunakan metode **`window.print()`** dikombinasikan dengan media query **CSS Print**.

---

## 1. Konsep CSS Print Media Query

Kita bisa menuliskan instruksi CSS khusus yang **hanya aktif saat kertas masuk ke mesin printer** (atau disimpan sebagai PDF) menggunakan tag `@media print`:
*   Kita menyembunyikan sidebar admin, tombol menu header, dan tombol "Cetak" (`display: none`) agar tidak ikut mengotori kertas print.
*   Kita memaksa margin halaman bersih polos dan melarang gambar background terpotong setengah halaman menggunakan `page-break-inside: avoid`.

---

## 2. Studi Kasus PMI: Membuat Lembar Surat Jalan Logistik Darah

Mari kita buat halaman laporan cetak surat jalan di berkas **`pages/cito/cetak-[id].vue`**.

```html
<!-- pages/cito/cetak-[id].vue -->
<template>
  <div class="max-w-xl mx-auto p-8 bg-white min-h-screen space-y-8 print:p-0">
    
    <!-- HEADER SURAT JALAN (Logo PMI & Alamat Kop Surat) -->
    <div class="flex items-center gap-4 border-b-4 border-double border-slate-800 pb-4">
      <span class="text-4xl text-red-600">🩸</span>
      <div>
        <h1 class="text-base font-black uppercase text-slate-800 tracking-wider">Unit Donor Darah PMI Provinsi Lampung</h1>
        <p class="text-[10px] text-slate-500 mt-0.5">Jl. Sam Ratulangi No. 105, Bandar Lampung | Telp: (0721) 123456</p>
      </div>
    </div>

    <!-- DETAIL TRANSAKSI -->
    <div class="space-y-3">
      <h3 class="text-center font-bold text-sm tracking-widest uppercase text-slate-800">Surat Pengiriman Logistik Darah CITO</h3>
      
      <div class="grid grid-cols-2 gap-4 text-xs pt-4">
        <div class="space-y-1">
          <p class="text-slate-400">Target Penerima:</p>
          <strong class="text-slate-800 font-bold block text-sm">{{ rsTarget }}</strong>
        </div>
        <div class="space-y-1 text-right">
          <p class="text-slate-400">Nomor Transaksi:</p>
          <strong class="text-slate-800 font-mono font-bold block text-sm">#CITO-{{ idTransaksi }}</strong>
        </div>
      </div>
    </div>

    <!-- TABEL LOGISTIK -->
    <table class="w-full text-xs text-left border-collapse border border-slate-200">
      <thead>
        <tr class="bg-slate-50 border-b border-slate-200">
          <th class="py-2.5 px-4 border-r border-slate-200">Item Logistik</th>
          <th class="py-2.5 px-4 text-center">Jumlah Kantong</th>
        </tr>
      </thead>
      <tbody>
        <tr class="border-b border-slate-200">
          <td class="py-2.5 px-4 border-r border-slate-200">Kantong Darah Golongan {{ golDarah }}</td>
          <td class="py-2.5 px-4 text-center font-bold">{{ jumlahKantong }} Bag</td>
        </tr>
      </tbody>
    </table>

    <!-- AREA TANDA TANGAN -->
    <div class="grid grid-cols-2 gap-8 pt-12 text-center text-xs">
      <div class="space-y-12">
        <p class="text-slate-500">Petugas Pengirim UDD,</p>
        <div class="w-32 border-b border-slate-400 mx-auto"></div>
      </div>
      <div class="space-y-12">
        <p class="text-slate-500">Petugas Penerima RS,</p>
        <div class="w-32 border-b border-slate-400 mx-auto"></div>
      </div>
    </div>

    <!-- TOMBOL CETAK (Disembunyikan Otomatis Saat Dicetak Kertas) -->
    <div class="pt-6 border-t border-slate-100 flex justify-end gap-2 print:hidden">
      <button 
        @click="kembali" 
        class="bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-xs px-4 py-2 rounded-xl transition-all"
      >
        Kembali
      </button>
      <button 
        @click="triggerCetak" 
        class="bg-red-500 hover:bg-red-600 text-white font-bold text-xs px-5 py-2 rounded-xl shadow-md transition-all"
      >
        🖨️ Mulai Cetak PDF
      </button>
    </div>

  </div>
</template>

<script setup>
import { useRoute } from 'vue-router';
import { ref } from 'vue';

definePageMeta({
  // Kita matikan layout sidebar admin default agar halaman cetak bersih putih polos
  layout: false 
});

const route = useRoute();
const idTransaksi = route.params.id || '101';

const rsTarget = ref('RSU Abdul Moeloek');
const golDarah = ref('O');
const jumlahKantong = ref(5);

const triggerCetak = () => {
  // Pemicu dialog print bawaan sistem operasi peramban
  window.print();
};

const kembali = () => {
  navigateTo('/cito');
};
</script>

<style>
/* CSS KHUSUS YANG HANYA AKTIF SAAT KERTAS MASUK KE PRINTER */
@media print {
  /* Sembunyikan elemen dekoratif yang tidak perlu tercetak di kertas */
  .print\:hidden {
    display: none !important;
  }
  
  body {
    background-color: #ffffff !important;
    color: #000000 !important;
  }
}
</style>
```

---

## 3. Latihan Soal Mandiri
1. Amati letak class `print:hidden` pada tombol kendali di atas.
2. Jelaskan fungsi dari modifikasi class prefix **`print:`** pada Tailwind CSS di atas dan apa akibat buruknya jika kita lupa menyisipkannya pada tombol "Mulai Cetak PDF".
3. Mengapa kita harus menonaktifkan layout sidebar admin default (`layout: false`) pada baris properti `definePageMeta` khusus untuk halaman cetak kop surat jalan ini?

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan Utility prefix print:
*   **Fungsi `print:hidden`**: Merupakan modifier visual bawaan Tailwind CSS. Di balik layar, Tailwind menerjemahkannya menjadi aturan CSS `@media print { .print\:hidden { display: none !important; } }`.
*   **Akibat jika lupa**: Tombol merah bertuliskan *"🖨️ Mulai Cetak PDF"* akan **ikut tercetak kotor di lembar kertas fisik** atau file hasil simpanan PDF kurir, menurunkan nilai kesopanan administrasi surat dinas resmi PMI Lampung.

### Jawaban 3: Alasan Mematikan Layout (layout: false)
*   **Menghindari Cetakan Sampah Layout**: Halaman cetak surat jalan ditujukan untuk menghasilkan satu lembar kertas HVS bersih putih polos. 
*   Jika layout default sidebar admin dan header dashboard tetap menyala, visual menu navigasi samping, daftar user login, dan footer dashboard akan ikut tergambar menciut merusak kertas cetakan, yang memboroskan tinta printer dan merusak fungsionalitas kop surat dinas. Mematikan layout menjamin area render benar-benar steril khusus untuk struktur surat jalan saja.
