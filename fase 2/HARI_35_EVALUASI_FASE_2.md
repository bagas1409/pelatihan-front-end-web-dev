# Hari 35: Evaluasi Fase 2 - Ujian Praktek Nuxt 3
## Tantangan Akhir: Merancang Navigasi Utama Dashboard Admin PMI Dengan Layout Dinamis & Sistem Auto-Import Komponen

Selamat datang di Hari 35! Ini adalah **hari terakhir dari Fase 2**. 

Selama 20 hari ke belakang, Anda telah mempelajari dasar Vue 3 Composition API, reaktivitas data (`ref`/`reactive`), two-way binding (`v-model`), rendering dinamis (`v-for`), penanganan event, komponen reusable (Props/Emits), kalkulasi memori (`computed`), watchers, routing otomatis Nuxt 3, transisi animasi, teleportasi elemen, hingga konsep server rendering.

Hari ini Anda ditantang untuk merakit seluruh pengetahuan tersebut menjadi satu kerangka aplikasi web dinamis berskala menengah yang terstruktur rapi.

---

## 1. Spesifikasi Proyek Ujian Akhir Fase 2

Anda wajib membuat sebuah proyek Nuxt 3 baru (atau menggunakan proyek latihan Hari 17) dan menyusun folder file-filenya dengan arsitektur berikut:

```
donorku-frontend/
├── assets/
│   └── css/
│       └── main.css             <-- Animasi Page Transition ditulis di sini
├── components/
│   ├── ui/
│   │   └── Button.vue           <-- Menggunakan defineProps (variant, disabled)
│   └── dashboard/
│       └── CardMetrik.vue       <-- Kartu reaktif dengan event emit jika diklik detail
├── layouts/
│   ├── default.vue              <-- Layout Dashboard UDD (Ada Sidebar & Header slot)
│   └── auth.vue                 <-- Layout Box Tengah khusus Login
├── pages/
│   ├── index.vue                <-- Beranda Dashboard (Tampilkan computed total stok)
│   ├── stok.vue                 <-- Form input v-model stok darah dengan tombol simpan
│   ├── relawan/
│   │   ├── index.vue            <-- Tabel v-for nama relawan + Link dinamis detail
│   │   └── [id].vue             <-- Halaman useRoute() untuk profil NIK relawan
│   └── login.vue                <-- Halaman login petugas (menggunakan definePageMeta layout: 'auth')
└── nuxt.config.ts               <-- Mengaktifkan pageTransition: { name: 'halaman', mode: 'out-in' }
```

---

## 2. Kriteria Wajib Penilaian Ujian Kelulusan

1.  **Navigasi SPA Tanpa Refresh (Bobot 20%)**:
    *   Seluruh perpindahan antar halaman (Dashboard $\rightarrow$ Stok $\rightarrow$ Relawan) wajib menggunakan `<NuxtLink>` dan berfungsi mulus tanpa ada kedipan memutar roda reload di tab peramban browser.
2.  **Animasi Transisi Halaman (Bobot 20%)**:
    *   Wajib aktif transisi memudar vertikal halus saat berpindah rute halaman. (Dikonfigurasi di `nuxt.config.ts` dan dihias di `main.css`).
3.  **Layout Terpisah yang Akurat (Bobot 20%)**:
    *   Halaman `/login` wajib bersih tanpa sidebar, sedangkan halaman `/stok` dan `/relawan` wajib terbungkus sidebar navigasi default secara otomatis.
4.  **Auto-Imports & Reusable Component (Bobot 20%)**:
    *   Memanggil komponen UI Button (`<UiButton>`) dan Card (`<DashboardCardMetrik>`) secara langsung tanpa menuliskan baris `import` manual di tag script.
5.  **Logika Dynamic Route & Computed (Bobot 20%)**:
    *   Halaman detail relawan `/relawan/701` mampu membaca ID dari URL params, dan halaman beranda utama mampu menjumlahkan total kantong darah UDD secara cerdas menggunakan `computed()`.

---

## 3. Cara Menguji Hasil Ujian Mandiri Anda

1.  Jalankan server pengembangan di terminal Anda:
    ```bash
    npm run dev
    ```
2.  Buka browser di `http://localhost:3000/login` $\rightarrow$ Pastikan layout bersih box tengah aktif.
3.  Klik tombol masuk login dinas $\rightarrow$ Pastikan halaman berpindah ke `/` (Beranda) secara halus tanpa kedip.
4.  Buka menu database relawan $\rightarrow$ Klik salah satu nama relawan $\rightarrow$ Pastikan alamat URL berubah menjadi `/relawan/[id]` dan isi teks profil berganti sesuai data parameternya.
5.  Tekan tombol F12 inspect element di browser Anda $\rightarrow$ Pastikan tidak ada pesan error berwarna merah di jendela konsol log browser Anda.

Jika kelima langkah pengujian di atas berjalan lancar tanpa hambatan, selamat! Anda dinyatakan **LULUS FASE 2** dengan predikat memuaskan. 

Anda siap melangkah masuk ke **Fase 3** untuk mengintegrasikan state management global **Pinia** dan mengelola penarikan data fetch API server backend PMI yang sesungguhnya.
```
[FASE 2: SINKRONISASI PEMROGRAMAN DEKLARATIF NUXT 3 SUKSES]
```
