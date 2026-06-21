# Hari 55: Evaluasi Fase 3 - Ujian Praktek Integrasi API & State
## Tantangan Akhir: Menghubungkan Form Registrasi & Tabel Logistik UDD dengan Server Backend Express.js

Selamat datang di Hari 55! Ini adalah **hari terakhir dari Fase 3**. 

Selama 20 hari ke belakang, Anda telah mempelajari mekanisme data fetching (`$fetch`, `useFetch`, `useLazyFetch`), memotong waktu loading paralel (`Promise.all`), menguji error hydration (`<ClientOnly>`), membangun sistem State Management Pinia (`stores/auth.js`) beserta penguncian kuki persisten, merancang validasi form skema **Zod**, membuat custom composable (`useApi`), menyematkan notifikasi toast (`vue3-toastify`), optimasi SEO Google, hingga membuat halaman error kustom (`error.vue`).

Hari ini Anda ditantang untuk menyatukan seluruh komponen reaktif tersebut menjadi satu alur aplikasi web logistik yang fungsional terhubung ke database server API.

---

## 1. Spesifikasi Proyek Ujian Akhir Fase 3

Anda wajib merekayasa ulang file halaman dan store Anda dengan spesifikasi interaksi berikut:

```
donorku-frontend/
├── error.vue                   <-- Halaman error kustom dengan clearError()
├── stores/
│   └── auth.js                 <-- Menyimpan Token JWT Dinas (Persist Cookie 7 Hari)
├── composables/
│   └── useApi.js               <-- Menyuntikkan header Authorization Bearer secara otomatis
├── plugins/
│   └── toastify.client.js      <-- Notifikasi melayang global
├── pages/
│   ├── login.vue               <-- Form login memanggil Actions loginPetugas Pinia
│   ├── stok.vue                <-- Tabel useApi('/stocks') dengan loading skeleton & filter client-side
│   └── relawan/
│       └── baru.vue            <-- Form registrasi relawan divalidasi Zod + $fetch POST /api/volunteers + Toast
```

---

## 2. Alur Pengujian Kelulusan Ujian (Wajib Dilakukan)

1.  **Skenario 1: Halaman Error 404**
    *   Buka alamat URL `/halaman-ngawur-pmi`.
    *   *Kriteria Kelulusan*: Layar wajib memotong visual default, menampilkan kotak merah bertuliskan **404 Halaman Tidak Ditemukan**, dan tombol kembali ke beranda wajib berfungsi tanpa mematikan reaktivitas RAM browser.
2.  **Skenario 2: Login Petugas & Cookies Persisten**
    *   Buka `/login` $\rightarrow$ Masukkan username & password.
    *   *Kriteria Kelulusan*: Token JWT tersimpan di Cookie peramban. Saat ditekan tombol refresh (F5), status login tidak terputus.
3.  **Skenario 3: CRUD Relawan dengan useApi & Validasi Live**
    *   Buka `/relawan/baru` $\rightarrow$ Ketik NIK kurang dari 16 angka $\rightarrow$ Muncul border merah dan peringatan error.
    *   Ketik NIK 16 angka $\rightarrow$ Border menyala hijau dan tombol daftar menyala aktif.
    *   Klik daftar $\rightarrow$ Data dikirim via API POST melalui helper `useApi`.
    *   *Kriteria Kelulusan*: Formulir kosong kembali, dan balon **Toast Notification** melayang di kanan atas layar monitor bertuliskan sukses menyimpan data.
4.  **Skenario 4: Tabel Filter Stok Darah**
    *   Buka halaman `/stok` $\rightarrow$ Tampilkan data stok yang diambil dari backend.
    *   *Kriteria Kelulusan*: Mengetik kata pencarian wilayah (contoh: "Metro") menyaring baris tabel secara instan di client.

---

## 3. Pesan Kelulusan Fase 3

Jika seluruh fungsi di atas berjalan dengan mulus tanpa memicu error merah di konsol log browser, selamat! Anda secara resmi dinyatakan **LULUS FASE 3** dengan nilai sempurna. 

Anda telah melompati gerbang tersulit dalam frontend development: menghubungkan visual antarmuka dengan database server backend secara aman. 

Di **Fase 4** (Hari 56 s.d Hari 75), kita akan mempelajari teknik manipulasi file, mengunggah foto profil, sistem proteksi rute middleware, pagination tabel, pencatatan ekspor file excel, dan optimasi performa loading tingkat lanjut.
```
[FASE 3: INTEGRASI DATA API & STATE MANAGEMENT GLOBAL SUKSES]
```
