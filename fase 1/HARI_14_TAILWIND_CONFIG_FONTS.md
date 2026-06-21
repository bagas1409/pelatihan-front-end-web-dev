# Hari 14: Integrasi Font Kustom & Konfigurasi Tailwind CSS
## Menyetel Font Premium Outfit & Memasang Tema Warna Kustom di tailwind.config.js

Selamat datang di Hari 14! Hari ini kita akan mempelajari cara melakukan kustomisasi tingkat lanjut pada framework Tailwind CSS menggunakan berkas **`tailwind.config.js`** (File Konfigurasi Tailwind).

Secara bawaan, Tailwind menggunakan font sans-serif standar sistem operasi komputer (seperti Arial/Segoe UI). Untuk membuat web admin DonorKu terasa premium, eksklusif, dan modern, kita akan belajar cara memasukkan font Google Fonts khusus (seperti **Outfit** atau **Inter**) dan menambahkan palet warna khas PMI (seperti warna merah donor darah khusus) ke dalam sistem token Tailwind.

---

## 1. Kegunaan File `tailwind.config.js`

### 💡 Analogi Dunia Nyata: "Buku Panduan Standar Merek (Brand Guidelines) UDD PMI"
Bayangkan Anda adalah pimpinan divisi humas PMI Lampung yang merancang logo, stempel, dan kop surat organisasi.
*   Anda tidak ingin staf UDD mencetak brosur dengan sembarang jenis font (seperti Comic Sans) atau warna merah yang berbeda-beda (merah jambu, merah pudar).
*   Anda membuat **Buku Panduan Resmi** yang mengunci aturan:
    *   Warna Merah PMI Lampung wajib berkode warna: `#e11d48` (Rose 600).
    *   Jenis tulisan resmi di semua dokumen wajib menggunakan font: **Outfit**.
*   Di dalam proyek web, buku panduan resmi tersebut adalah berkas **`tailwind.config.js`**. Dia bertugas mengunci parameter desain agar seluruh developer yang ikut menulis kode menghasilkan visual yang konsisten.

---

## 2. Struktur Konfigurasi Font & Warna Kustom

Berikut adalah contoh isi konfigurasi file `tailwind.config.js` untuk proyek DonorKu:

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  // 1. Menentukan file mana saja yang dipindai kelas Tailwind-nya
  content: [
    "./components/**/*.{js,vue,ts}",
    "./layouts/**/*.vue",
    "./pages/**/*.vue",
    "./plugins/**/*.{js,ts}",
    "./app.vue",
    "./error.vue",
  ],
  theme: {
    extend: {
      // 2. Menambahkan warna kustom khusus PMI
      colors: {
        pmi: {
          merah: '#e11d48',    // Merah Rose 600 resmi PMI
          gelap: '#0f172a',    // Slate 900 untuk sidebar
          kritis: '#dc2626',   // Red 600 untuk CITO
          aman: '#059669',     // Emerald 600 untuk stok aman
        }
      },
      // 3. Menambahkan rumpun font kustom (Font Family)
      fontFamily: {
        sans: ['Outfit', 'Inter', 'sans-serif'], // Outfit ditaruh di urutan prioritas pertama
      }
    },
  },
  plugins: [],
}
```

---

## 3. Langkah Menghubungkan Font Kustom (Google Fonts)

Agar browser pengunjung bisa membaca font Outfit, kita harus memuat font tersebut di baris atas file CSS utama kita (biasanya di `assets/css/tailwind.css` atau index HTML):

```css
/* assets/css/tailwind.css */
@import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700;900&family=Inter:wght@400;500;600;700&display=swap');

@tailwind base;
@tailwind components;
@tailwind utilities;
```

Setelah disetel, kelas font bawaan `font-sans` di Tailwind secara otomatis akan beralih merender font **Outfit** yang sangat modis dan elegan secara global.

Untuk memanggil warna merah kustom PMI Lampung, kita tinggal memanggil kelas utilitas:
*   `bg-pmi-merah` $\rightarrow$ setara `background-color: #e11d48`
*   `text-pmi-kritis` $\rightarrow$ setara `color: #dc2626`
*   `border-pmi-aman` $\rightarrow$ setara `border-color: #059669`

---

## 4. Latihan Soal Mandiri
1. Amati file konfigurasi `tailwind.config.js` di atas.
2. Jelaskan perbedaan penulisan kustomisasi di dalam blok `theme: { extend: { ... } }` dengan menulis langsung di dalam blok `theme: { ... }` tanpa kata kunci `extend`.
3. Tuliskan baris tag HTML tombol "Daftar Donor" yang menggunakan warna latar belakang merah kustom PMI (`bg-pmi-merah`) dan font tebal Outfit bawaan.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Aturan Extend vs Override di Tailwind
*   **Menulis di dalam `theme: { extend: { ... } }`**: Berfungsi **Menambahkan** token kustom kita ke dalam daftar bawaan Tailwind tanpa menghapus kelas bawaan. Pilihan warna bawaan Tailwind (seperti `bg-blue-500`, `bg-emerald-100`) akan tetap bisa digunakan berdampingan dengan `bg-pmi-merah`.
*   **Menulis langsung di dalam `theme: { ... }` (Tanpa extend)**: Berfungsi **Menimpa Total (Override)**. Tailwind akan menghapus seluruh daftar warna dan ukuran bawaan pabrik mereka, dan hanya menyisakan warna kustom yang kita tulis. Hal ini dilarang kecuali Anda ingin merancang design system kustom 100% dari nol karena seluruh kelas warna bawaan Tailwind akan langsung tidak bisa digunakan (error).

### Jawaban 3: Implementasi Tombol Kustom:
```html
<!-- 
  bg-pmi-merah -> Memanggil token warna resmi pmi.merah (#e11d48)
  font-sans -> Otomatis menggunakan Outfit karena sudah dipetakan di fontFamily.sans
-->
<button class="bg-pmi-merah text-white font-sans font-bold px-6 py-3 rounded-xl hover:bg-red-700 transition-colors">
  Daftar Donor Baru
</button>
```
Dengan tombol ini, visual tombol akan sangat konsisten dengan identitas resmi visual PMI Lampung.
