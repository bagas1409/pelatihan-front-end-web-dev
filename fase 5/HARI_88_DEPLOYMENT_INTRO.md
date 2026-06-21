# Hari 88: Pengenalan Layanan Server Deployment
## Memahami Ekosistem Vercel, Netlify, & Server Mandiri VPS Untuk Menghosting Aplikasi Nuxt 3

Selamat datang di Hari 88! Hari ini kita akan mempelajari berbagai pilihan infrastruktur server internet untuk **Menghosting / Mendeploy** aplikasi website frontend Nuxt 3 kita ke dunia online agar bisa diakses oleh masyarakat umum dari mana saja.

Kita memiliki tiga pilihan kategori server hosting yang populer digunakan di industri startup dan instansi pemerintahan, masing-masing memiliki kelebihan dan kekurangan:

---

## 1. Tiga Kategori Utama Deployment Server

### A. Server Cloud Tanpa Server (Serverless / Edge - Contoh: Vercel & Netlify)
Layanan hosting modern yang dirancang khusus untuk mempermudah developer frontend mendeploy kode secara otomatis hanya dengan menghubungkan repositori GitHub.
*   **Kelebihan**: 100% Gratis untuk skala kecil, otomatis mengelola sertifikat SSL HTTPS gratis, setup sangat cepat (di bawah 2 menit), dan didukung jaringan server global (*Edge Network*) yang super cepat.
*   **Kekurangan**: Terikat batasan waktu eksekusi fungsi (*Serverless Execution Limits*). Kurang cocok jika web memproses kalkulasi data raksasa berdurasi jam-an.

### B. Virtual Private Server (VPS - Contoh: DigitalOcean, Linode, Biznet Gio)
Kita menyewa satu unit komputer server virtual utuh yang terletak di data center (misalnya data center Biznet di Jakarta). Kita mengontrol penuh sistem operasi Linux (Ubuntu/Debian) di dalamnya.
*   **Kelebihan**: Bebas menginstal database apa saja, tidak ada limitasi durasi eksekusi, sangat tangguh untuk memproses sistem internal pemerintahan.
*   **Kekurangan**: Berbayar bulanan (tidak ada gratisan), setup manual yang rumit (wajib mengerti terminal Linux, setting Nginx reverse proxy, dan setup keamanan firewall secara manual).

### C. Static Web Hosting (Jamstack - Contoh: GitHub Pages, Cloudflare Pages)
Halaman web dikonversi menjadi file HTML statis polos menggunakan perintah `nuxt generate` (tanpa SSR dinamis di server).
*   **Kelebihan**: Gratis selamanya, mampu menahan jutaan pengunjung tanpa resiko server tumbang (*High Scalability*).
*   **Kekurangan**: Tidak mendukung fitur dinamis seperti rute dinamis serverless, middleware server, atau rendering layout bersyarat dari sisi server Node.js.

---

## 2. Pilihan Terbaik untuk Proyek PMI Lampung

Untuk portal dinas **DonorKu PMI**, kita akan menggunakan **Vercel** karena:
1.  **Dukungan Nuxt 3 Bawaan**: Vercel didirikan oleh tim yang bekerja erat dengan pembuat Nuxt. Konfigurasi perakitan serverless Nitro berjalan 100% otomatis tanpa error.
2.  **Serverless Server-Side Rendering (SSR)**: Kita tetap bisa menggunakan fitur middleware otentikasi dinamis dan API Fetch tanpa kehilangan keunggulan kecepatan loading.

Di Hari 89, kita akan mempraktikkan proses deployment gratis ke Vercel secara nyata.

---

## 3. Latihan Soal Mandiri
1. Amati 3 kategori hosting di atas.
2. Jika website PMI Lampung hanya berupa **Landing Page Profil UDD statis berisi alamat dan sejarah PMI** (tanpa fitur login admin, tanpa database relawan, tanpa lonceng CITO), sebutkan kategori hosting mana yang paling efisien dipilih dari segi biaya operasional bulanan.
3. Jelaskan mengapa hosting Vercel sangat disukai oleh tim frontend developer saat bekerja menggunakan Git (GitHub / GitLab).

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pemilihan Static Web Hosting (Jamstack)
*   **Kategori Terpilih**: **Static Web Hosting (Jamstack - contoh: GitHub Pages / Cloudflare Pages)**.
*   **Alasan**: Halaman profil UDD murni berisi informasi statis yang jarang berubah dan tidak membutuhkan database dinamis. Mengompilasinya menjadi HTML statis polos (`nuxt generate`) lalu menaruhnya di GitHub Pages/Cloudflare Pages bersifat **100% Gratis selamanya**, tahan banting terhadap lonjakan trafik, dan bebas dari ancaman retasan database karena tidak ada server database yang aktif berjalan di belakangnya.

### Jawaban 3: Integrasi Otomatis CI/CD Git
*   **Deployment Otomatis (Continuous Integration & Deployment)**: Vercel terintegrasi erat dengan GitHub. Kapanpun tim programmer melakukan perintah `git push` ke repositori GitHub, robot Vercel akan mendeteksi perubahan tersebut secara real-time.
*   Robot Vercel akan langsung mengunduh kode terbaru, menjalankan perintah kompilasi `build`, dan merilis versi terbaru website PMI Lampung ke internet dalam hitungan detik secara otomatis tanpa mengharuskan developer mengunggah file manual menggunakan FTP FileZilla lagi.
