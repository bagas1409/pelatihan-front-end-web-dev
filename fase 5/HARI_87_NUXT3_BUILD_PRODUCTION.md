# Hari 87: Kompilasi Rilis Akhir (Build Production)
## Mempersiapkan Bundling Kode Dan Memverifikasi Hasil Kompilasi Folder .output

Selamat datang di Hari 87! Hari ini kita akan mempelajari cara melakukan **Kompilasi Rilis Akhir** (Build Production Bundle) untuk aplikasi frontend Nuxt 3 kita.

Selama tahap pengembangan coding (Development), kita menjalankan perintah `npm run dev`. Perintah tersebut memproses perakitan kode secara mentah dan menyalakan server pemantau perubahan berkas (*Hot Module Replacement*) yang memakan banyak tenaga memori RAM. Untuk dirilis ke internet agar diakses oleh publik, kita wajib mengompres, menghapus baris komentar dinas, menggabungkan kepingan CSS, dan merakit ulang seluruh file web menjadi bundel statis/server yang super ringan menggunakan perintah `build`.

---

## 1. Perbedaan Lingkungan Dev vs Production

| Aspek | Development (`npm run dev`) | Production (`npm run build`) |
| :--- | :--- | :--- |
| **Kecepatan Browser** | Sedang (karena memuat map debug yang tebal). | Sangat Cepat (sudah dikompresi / minified). |
| **Ukuran Berkas** | Sangat Besar (memuat seluruh modul un-used). | Sangat Kecil (hanya modul terpakai / tree-shaken). |
| **Pesan Error** | Sangat Detail (untuk mempermudah debugging coder). | Tersembunyi (demi keamanan sistem). |

---

## 2. Cara Melakukan Build di Nuxt 3

Buka terminal root proyek Nuxt 3 Anda, lalu jalankan perintah kompilasi bawaan berikut:

```bash
npm run build
```

Setelah perintah dijalankan, Vite bundler bawaan Nuxt akan bekerja keras memilah file dan menciptakan satu folder baru bernama **`.output/`** di root direktori proyek Anda.

---

## 3. Menjelajahi Struktur Folder Hasil Kompilasi `.output/`

Folder `.output/` berisi seluruh kode rakitan hasil kompilasi siap kirim ke server cloud. Mari kita bedah isinya:

```
donorku-frontend/
└── .output/
    ├── server/             <-- Berisi file JavaScript Nitro engine penanganan Server SSR
    │   └── index.mjs       <-- File pintu masuk utama server Node.js dinas
    └── public/             <-- Berisi file aset statis (HTML, CSS terkompresi, WebP gambar)
        └── _nuxt/          <-- Berisi kepingan JS chunks rakitan yang sudah diacak namanya
```

*   **Keamanan Nama Berkas (Cache Busting)**: Di dalam folder `public/_nuxt/`, Anda akan mendeteksi file JS dinamai acak, contoh: `entry.C4a8b9f.js`. Penambahan kode acak di tengah nama file bertujuan mencegah browser menyimpan cache file lama saat kita merilis pembaruan baru (*Cache Busting*).

---

## 4. Cara Menguji Build Lokal Sebelum Deploy

Untuk memastikan hasil kompilasi kita berjalan normal tanpa error merembet, kita bisa menyalakan server simulasi rilis di komputer lokal kita menggunakan perintah:

```bash
node .output/server/index.mjs
```
Aplikasi web Anda akan aktif menyala di port `3000` (atau port dinamis). Silakan klik menu-menu di browser untuk memverifikasi keselarasan visual halaman.

---

## 5. Latihan Soal Mandiri
1. Amati struktur berkas di dalam folder `.output` di atas.
2. Jelaskan kegunaan dari file `.output/server/index.mjs` saat dipasang di server web VPS dan mengapa kita tidak perlu lagi menginstal dependensi folder `node_modules` mentah yang berukuran gigabyte di dalam server produksi VPS tersebut.
3. Sebutkan apa fungsi dari pengacakan nama berkas (seperti `entry.C4a8b9f.js`) bagi kenyamanan petugas admin saat kita baru saja meng-update tampilan menu sidebar logistik di server.

---

## 6. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Keunggulan Nitro Server Bundle
*   **Engine Mandiri (Self-contained Server)**: Nitro Engine bawaan Nuxt 3 mengompilasi dan menggabungkan seluruh kode dependensi JavaScript yang dibutuhkan ke dalam satu file tunggal `.output/server/index.mjs`.
*   **Efisiensi Server**: Kita **tidak perlu lagi mengunggah folder `node_modules`** atau menjalankan perintah `npm install` kembali di komputer server VPS. Kita cukup mengunggah folder `.output/` yang berukuran kecil (hanya beberapa megabyte) dan langsung mengeksekusi perintah `node index.mjs` untuk menyalakan portal dinas PMI, menghemat ruang penyimpanan harddisk server secara drastis.

### Jawaban 3: Mencegah Bug Cache Browser (Cache Busting)
*   **Pembaruan Instan**: Browser client memiliki sifat menyimpan file JavaScript statis di harddisk lokal laptop agar pemuatan berikutnya terasa cepat (*Browser Caching*).
*   Jika nama file JavaScript bersifat statis (contoh: `entry.js`), saat kita memperbarui visual sidebar di server, browser petugas **akan tetap menggunakan file `entry.js` lama** yang tersimpan di harddisk mereka, menyebabkan perubahan tampilan tidak muncul di layar. Pengacakan nama berkas menjadi `entry.C4a8b9f.js` memaksa browser menyadari ada nama file baru, mengunduhnya seketika, dan menyajikan pembaruan visual teranyar tanpa mengharuskan petugas membersihkan riwayat browser secara manual.
