# Hari 86: Audit Performa dengan Google Lighthouse
## Mengukur Kecepatan Muat, Aksesibilitas, & Praktek Terbaik SEO Portal DonorKu PMI

Selamat datang di Hari 86! Hari ini kita akan mempelajari cara melakukan **Audit Performa & SEO** menggunakan alat bantu bawaan Google Chrome: **Google Lighthouse**.

Sebelum kita merilis kode frontend Nuxt 3 ke server internet publik, kita wajib memastikan website kita tidak berat, mudah ditemukan di mesin pencarian Google, serta ramah bagi penyandang disabilitas (*Accessibility*). Google Lighthouse adalah robot pemeriksa otomatis yang akan menilai kualitas website kita dengan skor skala `0` hingga `100`.

---

## 1. Empat Parameter Penilaian Google Lighthouse

Lighthouse mengukur kinerja website berdasarkan 4 pilar utama:
1.  **Performance (Performa)**: Seberapa cepat elemen pertama tergambar di layar (`First Contentful Paint`) dan seberapa lama browser macet karena mengeksekusi JavaScript yang berat.
2.  **Accessibility (Aksesibilitas)**: Apakah kontras warna tulisan mudah dibaca oleh mata yang kurang sehat, dan apakah tag HTML bersahabat bagi pembaca layar tunanetra (*Screen Readers*).
3.  **Best Practices (Praktek Terbaik)**: Keamanan situs web (seperti penggunaan HTTPS) dan kepatuhan terhadap standar sintaks HTML modern.
4.  **SEO (Search Engine Optimization)**: Kelengkapan meta tag deskripsi, title unik, dan struktur heading `<h1>` s.d `<h6>` yang tertata rapi.

---

## 2. Cara Menjalankan Audit Lighthouse di Google Chrome

1.  Buka aplikasi website Anda di browser Google Chrome (disarankan dalam mode penyamaran / **Incognito Mode** agar data extension Chrome tidak mengacaukan skor penilaian).
2.  Klik kanan di layar $\rightarrow$ Pilih **Inspect (Periksa)**.
3.  Pada baris menu tab developer tools kanan atas, klik tombol **Lighthouse** (jika tidak terlihat, klik tanda panah ganda `>>` untuk memunculkannya).
4.  Setel konfigurasi: Kategori $\rightarrow$ Centang semua, Device $\rightarrow$ Pilih **Mobile** (karena Google mengutamakan kecepatan di perangkat HP).
5.  Klik tombol **Analyze page load (Analisis Pemuatan Halaman)**.
6.  Tunggu sekitar 10-20 detik sampai robot Google selesai mensimulasikan pemuatan halaman dan menerbitkan skor warna (Merah: 0-49, Oranye: 50-89, Hijau: 90-100).

---

## 3. Cara Mengatasi Skor Merah Umum pada Nuxt 3

Berikut adalah rangkuman solusi cepat yang wajib dikuasai jika skor audit Lighthouse Anda bernilai rendah:

*   **Penyebab: "Image elements do not have explicit width and height"**
    *   *Solusi*: Wajib menyertakan properti lebar dan tinggi piksel asli pada tag gambar: `<NuxtImg src="..." width="300" height="200" />` guna menghindari pergeseran layout.
*   **Penyebab: "Links do not have a descriptive text"**
    *   *Solusi*: Hindari menulis link hanya berisi ikon kosong. Tambahkan atribut `aria-label`: `<NuxtLink to="/profil" aria-label="Buka Profil Anggota">👤</NuxtLink>`.
*   **Penyebab: "Document does not have a meta description"**
    *   *Solusi*: Ingat kembali pelajaran Hari 53! Selalu pasang composable `useSeoMeta()` di setiap file halaman utama Anda.

---

## 4. Latihan Soal Mandiri
1. Amati 4 parameter penilaian Lighthouse di atas.
2. Jelaskan mengapa kita disarankan menjalankan audit Lighthouse menggunakan mode penyamaran (**Incognito Mode**) browser daripada menggunakan mode tab browser biasa.
3. Sebutkan apa akibat buruknya bagi performa skor Lighthouse jika kita menumpuk 15 library pihak ketiga (plugins client-only) yang tidak perlu di aplikasi Nuxt kita.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Alasan Pentingnya Incognito Mode
*   **Akurasi Skor**: Tab browser biasa memuat puluhan ekstensi/addons yang kita instal (seperti AdBlocker, password manager, dll). Ekstensi-ekstensi tersebut secara fisik menyuntikkan script JavaScript tambahan di balik layar ke setiap website yang kita buka. 
*   Jika audit dijalankan di tab biasa, Lighthouse akan ikut menghitung durasi eksekusi JavaScript ekstensi tersebut sebagai bagian dari kelambatan website kita, menghasilkan skor performa palsu yang merosot merah (*False Negative Score*). Mode Incognito menonaktifkan seluruh ekstensi sehingga proses audit berjalan steril.

### Jawaban 3: Bahaya Bloating Library
*   **Peningkatan Ukuran Bundle (Bundle Bloat)**: Menumpuk belasan library eksternal yang di-load di sisi client akan membongkar tumpukan file JS raksasa yang harus diunduh dan diterjemahkan oleh CPU browser.
*   Hal ini akan meningkatkan waktu **Time to Interactive (TTI)** secara dramatis, menyebabkan layar HP petugas membeku macet tidak merespon klik tombol di detik-detik awal pemuatan halaman, dan menyeret skor Performance Lighthouse merosot jatuh ke zona merah.
