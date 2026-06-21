# Hari 69: Efek Animasi Pulsasi CITO (Tailwind Animate)
## Meningkatkan Kepekaan Antarmuka Dengan Efek Berkedip Halus & Animasi Ping Pada Alert Darurat

Selamat datang di Hari 69! Hari ini kita akan mempelajari implementasi **Micro-animations** (Animasi Mikro) untuk meningkatkan kepekaan visual (*Visual Hierarchy*) pada antarmuka admin PMI Lampung.

Saat mendeteksi status CITO kritis, petugas tidak boleh melewatkannya karena menyangkut keselamatan nyawa pasien. Kita akan merancang kartu peringatan status CITO berkedip halus menggunakan utility class Tailwind CSS **`animate-pulse`** dan **`animate-ping`** tanpa perlu menulis ratusan baris kode CSS `@keyframes` manual.

---

## 1. Mengenal Animasi Bawaan Tailwind CSS

Tailwind CSS menyediakan kelas animasi transisi siap pakai:
*   **`animate-ping`**: Membuat elemen membesar memudar secara melingkar seperti efek radar/sinyal sonar. Sangat cocok untuk titik notifikasi baru.
*   **`animate-pulse`**: Membuat elemen meredup-terang secara halus berulang-kali (efek bernapas). Sangat pas untuk visualisasi loading skeleton atau peringatan bahaya yang mendesak.

---

## 2. Studi Kasus PMI: Komponen Alert CITO di Dashboard Utama

Mari kita buat komponen banner peringatan CITO darurat di berkas **`components/dashboard/AlertCitoDinas.vue`**.

```html
<!-- components/dashboard/AlertCitoDinas.vue -->
<template>
  <div 
    v-if="daruratActive"
    class="relative p-6 bg-red-500 rounded-3xl text-white shadow-lg overflow-hidden border border-red-400 flex items-center justify-between"
  >
    <!-- Efek Cahaya Radar Latar Belakang (Animate Ping) -->
    <div class="absolute -top-4 -left-4 w-12 h-12 bg-white/20 rounded-full animate-ping pointer-events-none"></div>

    <!-- Konten Teks (Animate Pulse agar berkedip halus bagai napas) -->
    <div class="flex items-center gap-4 animate-pulse">
      <span class="text-3xl">⚠️</span>
      <div>
        <h4 class="text-sm font-extrabold tracking-wider uppercase">Panggilan CITO Darurat Aktif!</h4>
        <p class="text-xs text-red-100 mt-0.5 font-medium">
          Dibutuhkan segera {{ kantongButuh }} Kantong Darah Golongan {{ golDarah }} untuk RS Mitra Lampung.
        </p>
      </div>
    </div>

    <!-- Tombol Tindakan Cepat -->
    <button 
      @click="tanganiCito"
      class="bg-white hover:bg-red-50 text-red-600 font-extrabold text-[10px] uppercase tracking-wider px-5 py-3 rounded-2xl shadow-md transition-all active:scale-95 transform"
    >
      Tangani Sekarang
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const daruratActive = ref(true);
const golDarah = ref('AB');
const kantongButuh = ref(4);

const tanganiCito = () => {
  const { $toast } = useNuxtApp();
  $toast.success(`✓ Petugas UDD disiagakan untuk Golongan ${golDarah.value}!`);
  daruratActive.value = false; // Matikan banner setelah ditangani
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati letak penempatan kelas `animate-ping` dan `animate-pulse` pada template HTML di atas.
2. Jelaskan fungsi dari penambahan kelas **`pointer-events-none`** pada div absolut radar lingkaran di atas dan apa akibat buruknya bagi navigasi tombol jika kelas tersebut dihapus.
3. Sebutkan kelebihan dari segi performa browser menggunakan animasi bawaan CSS Tailwind (seperti `animate-pulse`) dibandingkan jika kita menuliskan fungsi manipulasi warna visual menggunakan interval JavaScript `setInterval(() => { ... merubah warna ... }, 500)`.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan pointer-events-none
*   **Fungsi `pointer-events-none`**: Menginstruksikan browser agar mengabaikan sensor klik mouse pada elemen tersebut, meloloskan klik ke elemen di lapisan bawahnya.
*   **Akibat jika dihapus**: Karena div radar tersebut memiliki posisi absolut dan melebar keluar di area pojok kiri atas card, jika petugas mencoba mengklik tombol atau link yang berada tepat di bawah area lingkaran radar tersebut, browser akan menghalangi klik tersebut karena mouse terhalang menabrak kotak div radar transparan di atasnya. Penambahan `pointer-events-none` memastikan kegunaan klik tombol tetap berfungsi 100% normal.

### Jawaban 3: Keunggulan CSS Animation vs JS Interval Animation
*   **Akselerasi Hardware GPU**: Animasi berbasis CSS dijalankan langsung oleh mesin rendering peramban (*Browser Rendering Engine*) menggunakan akselerasi kartu grafis komputer (GPU), menghasilkan visual yang sangat mulus (60 Frames Per Second).
*   **Menghindari Lagging**: Sebaliknya, menggunakan `setInterval` JavaScript untuk memanipulasi warna visual setiap milidetik akan memaksa CPU browser melakukan kalkulasi ulang layout berkali kali (*Reflow & Repaint*), yang membebani kinerja RAM browser dan membuat antarmuka web terasa patah-patah (*Lag*), khususnya di handphone dinas berspesifikasi rendah.
