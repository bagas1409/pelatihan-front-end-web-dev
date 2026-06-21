# Hari 17: Setup Project Nuxt 3 & Integrasi Tailwind CSS
## Menginisialisasi Proyek Baru Menggunakan Npx CLI & Menghubungkan Modul Tailwind Resmi

Selamat datang di Hari 17! Hari ini kita akan mempraktikkan langkah fisik membuat proyek baru **Nuxt 3** dan mengintegrasikan pustaka **Tailwind CSS** secara resmi menggunakan modul `@nuxtjs/tailwindcss`.

Nuxt 3 adalah framework tingkat tinggi (meta-framework) yang dibangun di atas Vue 3. Nuxt memanjakan developer dengan menyediakan fitur optimasi mesin pencari (SEO SSR), sistem impor komponen otomatis, dan pengaturan folder yang sangat teratur. Mari kita inisialisasi proyek DonorKu Frontend kita.

---

## 1. Langkah-Langkah Menginisialisasi Proyek Nuxt 3

### Langkah 1: Jalankan Perintah Pembuat Proyek
Buka terminal komputer Anda di folder pelatihan, lalu ketik perintah berikut (pastikan koneksi internet aktif karena npx akan mengunduh repositori template Nuxt resmi):

```bash
npx nuxi@latest init donorku-frontend
```

*Catatan: Jika muncul pertanyaan pemilihan package manager, gunakan tombol panah dan pilih **`npm`**. Jika ditanya tentang inisialisasi git, pilih **`yes`**.*

### Langkah 2: Masuk ke Folder & Instal Dependensi
Pindahkan terminal Anda ke folder proyek baru, lalu jalankan instalasi package:
```bash
# Pindah direktori
cd donorku-frontend

# Instal berkas node_modules
npm install
```

---

## 2. Mengintegrasikan Tailwind CSS secara Resmi

Untuk memasang Tailwind CSS di Nuxt 3, cara paling steril dan direkomendasikan adalah menggunakan modul resmi **`@nuxtjs/tailwindcss`**. Modul ini secara otomatis menyusun file konfigurasi Tailwind dan memproses build berkas secara efisien.

### Langkah 1: Instal Modul Pustaka
Ketik perintah ini di terminal proyek Anda:
```bash
npm install --save-dev @nuxtjs/tailwindcss
```

### Langkah 2: Daftarkan Modul di `nuxt.config.ts`
Buka berkas konfigurasi pusat Nuxt bernama **`nuxt.config.ts`** di VS Code Anda. Daftarkan modul Tailwind di dalam array `modules`:

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  compatibilityDate: '2024-04-03',
  devtools: { enabled: true },
  
  // Daftarkan modul di sini!
  modules: [
    '@nuxtjs/tailwindcss'
  ]
})
```

### Langkah 3: Jalankan Uji Coba Server Lokal
Ketik perintah menyalakan server lokal di terminal Anda:
```bash
npm run dev
```
Buka peramban browser Anda di alamat default: **`http://localhost:3000`**. Anda akan melihat tampilan beranda selamat datang bawaan Nuxt 3 yang bersih.

---

## 3. Studi Kasus PMI: Membuat CSS Utama & Uji Coba Kelas Tailwind

Mari kita buat file CSS utama untuk memuat font kustom Outfit Google Fonts yang telah kita rencanakan di Hari 14.

1.  Buat folder baru bernama **`assets/css/`** di dalam folder proyek Anda.
2.  Buat file baru di dalamnya bernama **`main.css`** dan isi dengan baris impor berikut:
    ```css
    /* assets/css/main.css */
    @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700;800;900&display=swap');

    /* Mengunci font global agar menggunakan Outfit */
    body {
        font-family: 'Outfit', sans-serif;
    }
    ```
3.  Buka kembali file `nuxt.config.ts` dan hubungkan file CSS tersebut ke konfigurasi pusat:
    ```typescript
    export default defineNuxtConfig({
      compatibilityDate: '2024-04-03',
      devtools: { enabled: true },
      modules: ['@nuxtjs/tailwindcss'],
      
      // Hubungkan berkas CSS utama
      css: [
        '~/assets/css/main.css'
      ]
    })
    ```

---

## 4. Latihan Soal Mandiri
1. Analisis pentingnya penambahan modul `@nuxtjs/tailwindcss` pada berkas konfigurasi `nuxt.config.ts` di atas.
2. Jika Anda ingin melakukan konfigurasi kustom warna atau breakpoint tambahan untuk Tailwind di dalam proyek Nuxt, sebutkan di berkas file manakah kustomisasi tersebut ditulis dan di mana letak lokasi file tersebut disimpan secara standar.
3. Sebutkan apa alamat port default server lokal yang digunakan oleh Nuxt 3 saat kita menjalankan perintah `npm run dev`.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1: Fungsi Modul Tailwind Nuxt
Modul `@nuxtjs/tailwindcss` bertugas sebagai jembatan otomatis. Tanpa modul ini, kita harus menginstal Tailwind CSS secara manual, mengonfigurasi PostCSS, menulis skrip build bundler Vite sendiri, dan mengimpor file CSS utilitas secara manual. Modul ini membungkus seluruh kerumitan tersebut sehingga Tailwind aktif instan di semua halaman web Nuxt hanya dengan satu baris registrasi modul.

### Jawaban 2: File Konfigurasi Tailwind di Nuxt
Kustomisasi warna dan font tambahan ditulis di dalam file **`tailwind.config.js`**.
*   **Lokasi Penyimpanan**: Secara standar, file ini ditaruh di root folder (direktori utama paling luar) proyek Nuxt 3 Anda (berdampingan dengan file `nuxt.config.ts`). Modul `@nuxtjs/tailwindcss` secara otomatis mendeteksi keberadaan file tersebut dan menggunakannya untuk proses compile.

### Jawaban 3: Port Default Nuxt 3
Alamat port default server lokal Nuxt 3 adalah **`port 3000`** (misal: `http://localhost:3000`), berbeda dengan port default React Vite yang biasanya menggunakan port `5173`.
