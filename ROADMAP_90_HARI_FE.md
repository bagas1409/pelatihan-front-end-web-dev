# 🗺️ ROADMAP PELATIHAN FRONTEND DEVELOPER (90 HARI)
## Kurikulum Siap Kerja: Menguasai Vue 3, Nuxt 3, & Tailwind CSS Melalui Integrasi API PMI (DonorKu)

Selamat datang di Roadmap Pembelajaran Frontend 90 Hari! Dokumen ini dirancang khusus untuk membawa Anda dari level nol (zero) hingga menjadi **Frontend Engineer siap kerja**. 

Seluruh pembelajaran akan berfokus pada ekosistem **Vue 3 / Nuxt 3** dan **Tailwind CSS**, dengan studi kasus nyata pembuatan **Admin Panel & Portal Relawan PMI (Indonesian Red Cross)**. Seluruh logika data akan diintegrasikan langsung dengan backend API Express.js & Database Prisma yang sudah dibangun sebelumnya.

---

## 📅 DAFTAR RINGKASAN FASE
1. **Fase 1 (Hari 01 - 15)**: Fondasi Dasar - HTML5, CSS3 Modern, JS ES6+, & Tailwind CSS
2. **Fase 2 (Hari 16 - 35)**: Fondasi Vue 3 (Composition API) & Arsitektur Nuxt 3
3. **Fase 3 (Hari 36 - 55)**: Data Fetching, State Management (Pinia), & Validasi Form
4. **Fase 4 (Hari 56 - 75)**: Integrasi API Medis, Autentikasi JWT, & Guard Middleware
5. **Fase 5 (Hari 76 - 90)**: Fitur Advanced (Peta, Export Berkas), Testing, & Production Deployment

---

## 🧱 FASE 1: FONDASI DASAR FRONTEND & TAILWIND CSS (HARI 01 - 15)
*Fokus: Memahami bagaimana halaman web dibangun, dimanipulasi dengan JS, dan dihias menggunakan Tailwind CSS Utility-First.*

*   **Hari 01**: Anatomi HTML5 & Semantic Web (Tag `<header>`, `<main>`, `<aside>`, `<footer>`) untuk aksesibilitas situs PMI Lampung.
*   **Hari 02**: CSS3 Box Model & Layouting Dasar (Margin, Padding, Border, Display Block vs Inline).
*   **Hari 03**: CSS Grid & Flexbox (Penyusunan tata letak kartu stok darah agar rapi sejajar).
*   **Hari 04**: Pengenalan Tailwind CSS (Instalasi CLI & Konsep Utility-First vs CSS Klasik).
*   **Hari 05**: Desain Layout Responsif dengan Tailwind (Breakpoint `sm:`, `md:`, `lg:`, `xl:` untuk tablet & laptop dinas).
*   **Hari 06**: Tailwind Pseudo-classes (Hover, Active, Focus, Group-Hover pada tombol CITO).
*   **Hari 07**: JavaScript ES6+ (Variabel `const`/`let`, Arrow Functions, Template Literals).
*   **Hari 08**: Manipulasi Array Modern (`map()`, `filter()`, `reduce()`, `find()` untuk mengolah data golongan darah).
*   **Hari 09**: Destructuring Object, Array, & Rest/Spread Operator di JavaScript.
*   **Hari 10**: Asynchronous JS (Promise, Async/Await untuk persiapan memanggil API).
*   **Hari 11**: Fetch API & Penanganan Error `try-catch` (Menembak API publik stok darah).
*   **Hari 12**: DOM Manipulation murni vs Konsep Reaktivitas Data modern.
*   **Hari 13**: Mendesain Layout Dashboard Admin PMI Pertama dengan Tailwind (Sidebar kiri, Navbar atas).
*   **Hari 14**: Integrasi Font Kustom (Google Fonts Outfit/Inter) & Konfigurasi `tailwind.config.js`.
*   **Hari 15**: Evaluasi Fase 1 - Merancang Halaman Statis Login & Registrasi Relawan PMI yang Responsif.

---

## 🏗️ FASE 2: VUE 3 COMPOSITION API & ARSITEKTUR NUXT 3 (HARI 16 - 35)
*Fokus: Bermigrasi ke framework Nuxt 3, memahami reaktivitas data, komponen reusable, dan sistem routing otomatis.*

*   **Hari 16**: Pengenalan Vue 3 & Perbedaan Composition API vs Options API.
*   **Hari 17**: Setup Project Nuxt 3 pertama dengan Vite & Integrasi Tailwind CSS (`@nuxtjs/tailwindcss`).
*   **Hari 18**: Struktur Direktori Nuxt 3 (`app.vue`, `pages/`, `components/`, `layouts/`, `public/`).
*   **Hari 19**: Reaktivitas di Vue 3 - Memahami `ref()` vs `reactive()` beserta kasus penggunaannya.
*   **Hari 20**: Pengikatan Data Ganda (Two-Way Data Binding) menggunakan `v-model` pada formulir PMI.
*   **Hari 21**: Direktif Kondisional (`v-if`, `v-else-if`, `v-else`) & Render List (`v-for` dengan atribut `:key`).
*   **Hari 22**: Event Handling (`v-on` / `@click`, `@submit.prevent`) untuk menangkap klik tombol simpan.
*   **Hari 23**: Komponen Reusable - Mengirim data dari Induk ke Anak menggunakan `defineProps`.
*   **Hari 24**: Komunikasi Anak ke Induk - Memicu event khusus menggunakan `defineEmits`.
*   **Hari 25**: computed Property - Menghitung jumlah kantong darah kritis secara otomatis di RAM.
*   **Hari 26**: watch & watchEffect - Memantau perubahan input untuk filter pencarian wilayah.
*   **Hari 27**: Nuxt File-Based Routing (Routing otomatis berdasarkan susunan file di folder `pages/`).
*   **Hari 28**: Navigasi Antar Halaman di Nuxt menggunakan `<NuxtLink>` & router programatis `navigateTo()`.
*   **Hari 29**: Rute Dinamis (Dynamic Routing) menggunakan berkas `pages/event/[id].vue` untuk detail agenda donor.
*   **Hari 30**: Layout Dinamis di Nuxt - Mengatur `layouts/default.vue` (dashboard) vs `layouts/auth.vue` (login).
*   **Hari 31**: Nuxt Components Auto-Imports & Pengelompokan Folder Komponen.
*   **Hari 32**: Transisi Halaman & Animasi Element di Nuxt 3 (`pageTransition`).
*   **Hari 33**: Penggunaan Teleport di Vue 3 untuk membuat Modal Pop-up konfirmasi donor.
*   **Hari 34**: Penggunaan Server-Side Rendering (SSR) vs Static Site Generation (SSG) di Nuxt 3.
*   **Hari 35**: Evaluasi Fase 2 - Membuat struktur navigasi lengkap Dashboard Admin PMI (Home, Stok, Relawan, Event) tanpa koneksi API.

---

## ⚡ FASE 3: DATA FETCHING, STATE MANAGEMENT, & VALIDASI (HARI 36 - 55)
*Fokus: Mengambil data dari API backend, mengelola state aplikasi secara terpusat, dan membuat validasi form yang aman.*

*   **Hari 36**: Konsep Data Fetching di Nuxt 3 - Perbedaan `$fetch`, `useFetch`, & `useLazyFetch`.
*   **Hari 37**: Penggunaan `useAsyncData` untuk query data kompleks dari database.
*   **Hari 38**: Mengatasi Masalah Hydration Mismatch antara Server (SSR) dan Client di Nuxt.
*   **Hari 39**: Penanganan Status Fetching (Loading indicator, Error handler, & Refetch data).
*   **Hari 40**: Mengintegrasikan API Get Stok Darah (`GET /api/stocks`) ke tabel dashboard.
*   **Hari 41**: Membuat Filter Pencarian data stok golongan darah A/B/O/AB secara real-time di client.
*   **Hari 42**: Pengenalan Pinia (State Management resmi Vue 3 pengganti Vuex).
*   **Hari 43**: Membuat Store Pinia Pertama (`stores/auth.js`) untuk menyimpan info sesi login admin.
*   **Hari 44**: Persist State Pinia - Menyimpan otomatis data store ke localStorage menggunakan plugin persist.
*   **Hari 45**: Validasi Formulir di Nuxt menggunakan library **FormKit** atau **Vee-Validate**.
*   **Hari 46**: Integrasi Schema Validation **Zod** dengan Form Input di frontend.
*   **Hari 47**: Validasi Kolom Email, Password, & NIK KTP Relawan secara langsung saat mengetik.
*   **Hari 48**: Mengirim data form pendaftaran donor baru (`POST /api/donor`) ke server backend.
*   **Hari 49**: Custom Composables di Nuxt 3 - Memecah logika fetch agar bisa dipakai berulang kali.
*   **Hari 50**: Membuat Composable `useApi` sebagai pembungkus default base URL API.
*   **Hari 51**: Nuxt Plugins - Cara mendaftarkan library pihak ketiga (seperti Toast Notification).
*   **Hari 52**: Menampilkan Notifikasi Melayang (Toast) saat input data sukses atau gagal.
*   **Hari 53**: Mengatur SEO Meta Tags secara dinamis (`useHead`, `useSeoMeta`) untuk pencarian Google.
*   **Hari 54**: Penanganan Error Halaman Kustom - Membuat file `error.vue` untuk halaman 404 & 500.
*   **Hari 55**: Evaluasi Fase 3 - Membuat Form Registrasi & List Stok Darah terintegrasi dengan validasi Zod & composable fetch.

---

## 🔐 FASE 4: INTEGRASI API MEDIS, AUTH JWT, & SECURITY GUARD (HARI 56 - 75)
*Fokus: Mengamankan halaman admin menggunakan Middleware, mengelola token JWT, & melacak status darurat CITO.*

*   **Hari 56**: Memahami Alur Kerja JWT (JSON Web Token) di sisi Frontend.
*   **Hari 57**: Membuat Halaman Login Petugas UDD & Mengirim password terenkripsi.
*   **Hari 58**: Menyimpan Token JWT secara aman di Nuxt menggunakan `useCookie()` (lebih aman untuk SSR dibanding localStorage).
*   **Hari 59**: Setup Axios / Nuxt Interceptors untuk menyisipkan header `Authorization: Bearer <token>` di setiap request.
*   **Hari 60**: Menangani Token Expired secara otomatis di interceptor (Auto Redirect ke Login).
*   **Hari 61**: Nuxt Route Middleware - Melindungi rute `/dashboard/*` agar tidak bisa diakses sebelum login.
*   **Hari 62**: Otorisasi Menu Berbasis Role (Role-Based Menu) - Menyembunyikan menu setting jika role bukan `SUPER_ADMIN`.
*   **Hari 63**: Integrasi Fitur Registrasi Relawan Baru oleh Admin (`POST /api/relawan`).
*   **Hari 64**: Operasi CRUD Lengkap 1: Halaman kelola data Relawan (Read & Search).
*   **Hari 65**: Operasi CRUD Lengkap 2: Membuat modal form Tambah & Update data Relawan.
*   **Hari 66**: Operasi CRUD Lengkap 3: Fitur Hapus (Delete) data Relawan dengan konfirmasi modal aman.
*   **Hari 67**: Integrasi API Peta Distribusi Darah - Memuat daftar rumah sakit penerima donor.
*   **Hari 68**: Sinkronisasi Data Real-time - Menerapkan Polling Engine untuk mengecek lonceng notifikasi CITO darurat.
*   **Hari 69**: Membuat Komponen Peringatan CITO Berkedip (`animate-pulse`) di pojok header web admin.
*   **Hari 70**: Fitur Setujui Permintaan CITO Darurat (`PATCH /api/permintaan/:id`).
*   **Hari 71**: Integrasi Axios File Upload (Mengunggah foto profil relawan ke backend).
*   **Hari 72**: Penanganan Security Headers & Validasi Payload input dari ancaman serangan XSS di frontend.
*   **Hari 73**: Manajemen Loading Skeleton - Membuat efek kerangka memuat konten agar website terasa premium.
*   **Hari 74**: Mengoptimalkan Asset Visual (Mengompresi & menyajikan gambar banner format `.webp`).
*   **Hari 75**: Evaluasi Fase 4 - Simulasi Login -> Token disimpan di Cookie -> Masuk Dashboard -> CRUD Relawan -> Lonceng CITO Berdering.

---

## 🚀 FASE 5: ADVANCED COMPONENT, TESTING, & DEPLOYMENT (HARI 76 - 90)
*Fokus: Menambahkan fitur peta interaktif, melakukan pengujian kode, dan meluncurkan website ke internet publik.*

*   **Hari 76**: Peta Interaktif di Nuxt 3 - Mengintegrasikan Leaflet JS / Google Maps tanpa jQuery.
*   **Hari 77**: Merender Marker koordinat cabang UDD PMI dan RS Swasta secara dinamis di atas peta Leaflet.
*   **Hari 78**: Fitur Geocoding - Mengubah ketikan alamat admin menjadi titik koordinat lintang bujur.
*   **Hari 79**: Pembuatan Ekspor File di Frontend - Mengonversi tabel data relawan menjadi file **Excel** (`xlsx`).
*   **Hari 80**: Pembuatan Ekspor File di Frontend - Membuat tombol Cetak Kartu Anggota Relawan format **PDF** (`jspdf`).
*   **Hari 81**: Dark Mode / Light Mode di Nuxt 3 menggunakan modul `@nuxtjs/color-mode`.
*   **Hari 82**: Pengenalan Pengujian Unit (Unit Testing) di Vue menggunakan **Vitest** & `@vue/test-utils`.
*   **Hari 83**: Menulis Tes Unit pertama untuk menguji komponen tombol & komputasi computed stok.
*   **Hari 84**: Pengenalan End-to-End (E2E) Testing menggunakan **Playwright** / **Cypress**.
*   **Hari 85**: Menulis skenario E2E: Mengisi form login -> Klik tombol -> Masuk ke halaman dashboard utama.
*   **Hari 86**: Build Produksi Nuxt - Menjalankan perintah `npm run build` dan menganalisis ukuran bundle berkas.
*   **Hari 87**: Deployment ke Vercel (Menghubungkan Git Repository ke akun Vercel Cloud).
*   **Hari 88**: Setup Kustom Domain (Mengarahkan CNAME record domain dinas PMI ke DNS Vercel).
*   **Hari 89**: Konfigurasi SSL/TLS HTTPS gratis dari Let's Encrypt secara otomatis di Vercel.
*   **Hari 90**: Uji Coba Lapangan Akhir (E2E Live Cloud) - Menguji performa kecepatan web admin dan verifikasi alur data.

---

## 🎯 TARGET KELULUSAN PORTFOLIO (READY TO WORK)
Di akhir hari ke-90, Anda wajib memiliki **1 proyek aplikasi portofolio berskala produksi** yang di-deploy online:
*   **Nama Proyek**: *Admin Portal DonorKu PMI Lampung*
*   **Fitur Utama**: Multi-role Auth (JWT Cookie), Dashboard real-time (Polling CITO), CRUD Relawan & Stok Darah ter-indeks, Peta interaktif Leaflet, Ekspor berkas cetak PDF/Excel, dan Terintegrasi CI/CD Git Vercel.
