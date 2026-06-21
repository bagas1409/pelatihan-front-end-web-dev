# Hari 75: Evaluasi Fase 4 - Ujian Uji Coba CRUD & Keamanan
## Tantangan Akhir: Menyatukan Middleware Proteksi Rute, Operasi CRUD Relawan Lengkap, Upload Foto, Dan Polling Engine CITO

Selamat datang di Hari 75! Ini adalah **hari terakhir dari Fase 4**. 

Selama 20 hari terakhir, Anda telah naik kelas menjadi developer frontend tingkat menengah. Anda telah memecahkan masalah keamanan rute dengan **Middleware Rute** (`auth` & `guest`), menguasai siklus hidup Vue 3 (`onMounted` & `onUnmounted`) untuk menangani polling real-time 10 detik, merancang CRUD lengkap (Edit PUT, Hapus DELETE dengan modal teleport), mengamankan form dari serangan **XSS** (`DOMPurify`), mengunggah gambar (`FormData`), mendesain loading skeleton, dan optimasi format **WebP**.

Hari ini Anda ditantang menyatukan semua modul keamanan dan CRUD tersebut ke dalam satu simulasi portal dinas yang kokoh.

---

## 1. Spesifikasi Proyek Ujian Akhir Fase 4

Anda wajib memastikan seluruh struktur berkas di bawah ini saling berkomunikasi dengan aman tanpa memicu crash:

```
donorku-frontend/
├── middleware/
│   ├── auth.js             <-- Melarang masuk halaman dalam jika token kosong / expired
│   └── guest.js            <-- Melarang masuk halaman login jika token aktif terdeteksi
├── composables/
│   └── useApi.js           <-- Auto-inject Token JWT + HTTP Interceptor menangkap Error 401
├── components/
│   ├── skeleton/
│   │   └── StokSkeleton.vue <-- Animasi berdenyut abu-abu slate-200
│   └── dashboard/
│       ├── LoncengCito.vue  <-- Melakukan polling Cito 10 detik + mematikan interval onUnmounted
│       └── AlertCitoDinas.vue <-- Alert merah pulsasi dengan animate-pulse & pointer-events-none
├── pages/
│   ├── login.vue           <-- Form login (Terproteksi middleware guest)
│   ├── relawan/
│   │   ├── index.vue       <-- Tabel pagination + Tombol Hapus (DELETE + Modal Teleport)
│   │   ├── baru.vue        <-- Registrasi tervalidasi Zod
│   │   ├── edit/[id].vue   <-- Edit data (PUT + Initial Values + Validasi Zod)
│   │   ├── upload-foto.vue <-- Upload avatar KTP (FormData + URL.createObjectURL + Maks 2MB)
│   │   └── catatan.vue     <-- Render riwayat medis tervalidasi XSS DOMPurify
```

---

## 2. Alur Pengujian Kelulusan Ujian (Wajib Dilakukan)

1.  **Skenario 1: Pengujian Pos Satpam (Middleware)**
    *   Coba buka `/relawan` saat belum login $\rightarrow$ Harus ditendang ke `/login`.
    *   Lakukan login $\rightarrow$ Token masuk Cookie. Coba buka kembali `/login` $\rightarrow$ Harus ditolak dan dibelokkan masuk kembali ke beranda `/`.
2.  **Skenario 2: Token Kadaluarsa (Interceptor 401)**
    *   Simulasikan token JWT Anda kedaluwarsa atau rusak.
    *   Coba klik tombol hapus relawan atau navigasi menu $\rightarrow$ Server menolak dengan HTTP `401`.
    *   *Kriteria Kelulusan*: Sistem wajib mendeteksi error 401 tersebut di file `useApi.js`, secara otomatis memicu `authStore.logoutPetugas()`, memicu toast merah *"Sesi dinas berakhir"*, dan mengembalikan kursor layar ke halaman `/login`.
3.  **Skenario 3: CRUD Edit & Hapus Pengaman**
    *   Buka `/relawan` $\rightarrow$ Klik Edit $\rightarrow$ Kolom harus auto-fill berisi data lama dari API server backend.
    *   Buka `/relawan` $\rightarrow$ Klik Hapus $\rightarrow$ Harus muncul modal konfirmasi melayang. Klik Ya $\rightarrow$ Data terhapus di database server, toast sukses muncul, dan tabel memanggil `refresh()`.
4.  **Skenario 4: Upload Foto & Sanitasi XSS**
    *   Unggah gambar berukuran 5MB $\rightarrow$ Harus ditolak oleh validasi frontend dengan toast *"Maksimal 2MB"*.
    *   Buka `/relawan/catatan` $\rightarrow$ Ketik teks `<script>alert('hack')</script>` $\rightarrow$ Browser tidak boleh memunculkan alert (aman disaring DOMPurify).
5.  **Skenario 5: Polling & Animasi CITO**
    *   Buka beranda dashboard $\rightarrow$ Biarkan 10 detik $\rightarrow$ Lonceng notifikasi harus berkedip merah memanggil API polling baru. Banner merah CITO harus berdenyut halus (`animate-pulse`).

---

## 3. Pesan Kelulusan Fase 4

Jika seluruh interaksi pengujian di atas berjalan mulus tanpa melahirkan pesan error merah di konsol log browser, selamat! Anda dinyatakan **LULUS FASE 4** dengan predikat Developer Frontend Handal.

Anda telah menguasai seluruh aspek penanganan data kompleks dan keamanan di sisi client.

Di **Fase 5 (Fase Terakhir - Hari 76 s.d Hari 90)**, kita akan melangkah ke tahap akhir: visualisasi grafik data dinamis, integrasi peta Leaflet JS sebaran RS, pembuatan berkas cetak PDF laporan logistik darah, optimasi caching Nuxt `keep-alive`, dan proses deployment rilis production frontend ke server internet Vercel/Netlify.
```
[FASE 4: INTEGRASI CRUD KOMPLEKS & KEAMANAN FRONTEND SUKSES]
```
