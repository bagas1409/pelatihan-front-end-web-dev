# Hari 74: Optimasi Gambar Berformat WebP & NuxtImage
## Mempercepat Waktu Pemuatan Halaman Dengan Kompresi Gambar WebP Dinamis

Selamat datang di Hari 74! Hari ini kita akan mempelajari teknik **Optimasi Aset Visual** (Image Performance Optimization) menggunakan format modern **`WebP`** dan pengenalan modul **Nuxt Image**.

Saat membangun portal landing page PMI Lampung, kita seringkali memasang gambar banner besar atau ilustrasi donor darah. Menggunakan gambar format mentah `.png` atau `.jpg` beresolusi tinggi (misal berukuran 3 Megabyte) akan membebani kuota data petugas di lapangan dan menurunkan nilai performa Lighthouse web kita menjadi merah. Kita akan mempelajari cara mengompresi gambar menjadi format WebP yang sangat ringan namun tetap tajam.

---

## 1. Mengapa Memilih Format WebP?

*   **Kompresi Unggul**: Gambar format **WebP** buatan Google memiliki ukuran file **30% s.d 80% lebih kecil** dibandingkan JPEG atau PNG pada tingkat kualitas ketajaman gambar yang sama (*Lossless & Lossy Compression*).
*   **Mendukung Transparansi**: Berbeda dengan JPG yang tidak bisa transparan, WebP mendukung background transparan layaknya PNG, menjadikannya standar format terbaik untuk web modern saat ini.

---

## 2. Modul Nuxt Image (`@nuxt/image`)

Nuxt 3 menyediakan modul khusus bernama **Nuxt Image** yang bertugas mengonversi dan mengompresi seluruh gambar di website kita menjadi WebP secara otomatis di sisi server sebelum dikirim ke browser client.

### Langkah 1: Instalasi Modul
Jalankan perintah penginstalan modul di terminal root proyek Anda:
```bash
npm install -D @nuxt/image
```

### Langkah 2: Daftarkan di `nuxt.config.ts`
Buka konfigurasi Nuxt dan tambahkan `@nuxt/image` ke array modules:

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  compatibilityDate: '2024-04-03',
  modules: [
    '@nuxtjs/tailwindcss',
    '@nuxt/image' // Daftarkan modul di sini!
  ]
})
```

---

## 3. Studi Kasus PMI: Mengganti Tag `<img>` Biasa dengan `<NuxtImg>`

Setelah modul terpasang, kita disarankan mengganti penggunaan tag HTML biasa `<img>` menjadi komponen pintar **`<NuxtImg>`**:

### Skenario Lama (Mentah - Lambat & Besar):
```html
<img src="/images/banner-pmi-besar.png" class="w-full" />
```

### Skenario Baru (Optimum - WebP Otomatis & Cepat):
```html
<!-- 
  format="webp" -> Memaksa konversi format gambar menjadi WebP secara dinamis
  quality="80" -> Mengompres kualitas ketajaman di angka optimal 80% (menghemat 70% size file)
  loading="lazy" -> Gambar baru di-download jika posisinya sudah dekat di-scroll oleh user (Lazy Loading)
-->
<NuxtImg 
  src="/images/banner-pmi-besar.png" 
  format="webp"
  quality="80"
  loading="lazy"
  class="w-full rounded-3xl shadow-sm border"
/>
```

---

## 4. Latihan Soal Mandiri
1. Amati parameter `:quality="80"` dan `loading="lazy"` pada tag `<NuxtImg>` di atas.
2. Jelaskan fungsi dari parameter **`loading="lazy"`** pada optimasi kecepatan pemuatan awal website (*First Contentful Paint*) bagi pengguna laptop dengan kuota terbatas.
3. Sebutkan apa kelebihan format WebP dibandingkan format PNG saat digunakan untuk memuat foto profil petugas yang diunggah dari kamera HP.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan Lazy Loading Gambar
*   **Fungsi `loading="lazy"`**: Menunda pengunduhan file gambar oleh browser sampai posisi scroll pengguna mendekati koordinat gambar tersebut di layar.
*   **Keuntungan Performa**: Jika suatu halaman artikel UDD PMI memiliki 20 foto dokumentasi di bagian bawah halaman, browser **tidak akan mengunduh 20 gambar tersebut saat pertama kali website dibuka**. Browser hanya mengunduh 1 banner atas yang terlihat mata. Ini mempercepat waktu pemuatan halaman awal (*First Contentful Paint*) dan menghemat penggunaan kuota data internet petugas lapangan karena gambar di bawah tidak diunduh jika tidak di-scroll.

### Jawaban 3: Efisiensi Kompresi WebP vs PNG
*   **Ukuran Jauh Lebih Kecil**: Foto hasil kamera handphone biasanya berformat JPEG berukuran 4 s.d 8 Megabyte. Jika diubah ke format PNG, ukuran file bisa bertambah besar karena PNG dirancang untuk gambar grafis vektor tanpa kehilangan kompresi data (*Lossless*).
*   Mengonversinya ke format **WebP** dengan kualitas `80%` akan memotong ukuran foto profil tersebut dari **5 Megabyte menjadi hanya sekitar 300 Kilobyte saja** tanpa menurunkan detail wajah petugas secara kasat mata, menghemat beban kapasitas penyimpanan harddisk server backend database kita secara drastis.
