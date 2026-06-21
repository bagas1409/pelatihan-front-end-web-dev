# Hari 15: Evaluasi Fase 1 - Studi Kasus Mandiri
## Ujian Kemampuan Dasar: Merancang Halaman Formulir Registrasi Relawan DonorKu yang Responsif & Interaktif

Selamat datang di Hari 15! Hari ini adalah **hari terakhir dari Fase 1**.

Anda telah mempelajari dasar HTML5 semantik, CSS Box Model, Flexbox, Grid, filosofi Utility-First Tailwind CSS, manipulasi array modern ES6, hingga fungsi asinkron (fetch) untuk memanggil API backend. Hari ini adalah hari ujian mandiri untuk menguji sejauh mana Anda dapat menerapkan seluruh ilmu tersebut untuk membangun halaman antarmuka nyata.

---

## 1. Soal Tantangan Proyek Ujian Mandiri

Anda diminta untuk membangun sebuah halaman mandiri (Single Page) bertajuk **"Formulir Pendaftaran Donor Darah Mandiri - DonorKu"**. Halaman ini harus dirancang agar ramah dibuka di HP relawan di lapangan dan komputer dinas UDD.

### Kriteria Wajib Penilaian Ujian:

1.  **Struktur HTML5 Semantik (Hari 1 & 2)**:
    *   Wajib dibungkus dengan tag `<header>` untuk banner atas PMI Lampung, `<main>` untuk formulir utama, dan `<footer>` untuk informasi lisensi.
2.  **Layout Responsif Tailwind (Hari 3 & 5)**:
    *   Formulir harus bertumpuk **1 kolom di layar HP** (mobile-first), dan otomatis berubah menjadi **2 kolom sejajar di layar monitor laptop** (`md:` / `lg:`).
3.  **Interaktivitas Mikro Premium (Hari 4 & 6)**:
    *   Gunakan efek pendaran bingkai (`focus:ring`) berwarna merah pada semua kolom input (Nama Lengkap, NIK KTP, Pilihan Golongan Darah, Berat Badan).
    *   Berikan efek membal tertekan (`active:scale-95`) pada tombol submit pendaftaran.
4.  **Logika Input JS Modern (Hari 7 s.d 9)**:
    *   Buat penampung data (objek) di JavaScript untuk menyimpan inputan user.
    *   Gunakan teknik *Destructuring* untuk memisahkan data identitas (Nama, NIK) dengan data medis pendukung.
5.  **Simulasi Pengiriman API Asinkron (Hari 10 & 11)**:
    *   Saat tombol submit diklik, program wajib memicu fungsi asinkron (`async/await`) untuk menyimulasikan pengiriman data JSON ke URL API: `https://pmi-backend-api.onrender.com/api/donations`.
    *   Tampilkan notifikasi melayang (alert HTML kustom sederhana) bertuliskan *"✓ Pendaftaran Sukses"* setelah proses tunggu asinkron 2 detik selesai.

---

## 2. Draf Rancangan Kode Solusi

Berikut adalah kerangka solusi awal yang bisa Anda jadikan acuan. Lengkapi bagian-bagian logika JavaScript yang masih kosong untuk meluluskan ujian Anda.

```html
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Registrasi Pendonor Mandiri - DonorKu PMI</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Outfit Google Font -->
  <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@400;600;800&display=swap" rel="stylesheet">
  <style>
    body { font-family: 'Outfit', sans-serif; }
  </style>
</head>
<body class="bg-slate-50 min-h-screen flex flex-col justify-between">

  <!-- HEADER -->
  <header class="bg-white border-b border-slate-200 py-6 px-8 text-center">
    <span class="text-3xl">🩸</span>
    <h1 class="text-xl font-bold text-slate-800 mt-2">PMI PROVINSI LAMPUNG</h1>
    <p class="text-xs text-slate-500 uppercase tracking-wider mt-1">Formulir Pendaftaran Donor Darah Sukarela</p>
  </header>

  <!-- MAIN AREA (FORMULIR UTAMA) -->
  <main class="flex-1 max-w-2xl mx-auto w-full p-6">
    <div class="bg-white p-8 rounded-3xl border border-slate-200 shadow-sm space-y-6">
      
      <h2 class="text-lg font-bold text-slate-900 border-b border-slate-100 pb-4">
        Biodata & Riwayat Medis Singkat
      </h2>

      <!-- FORM DENGAN GRID RESPONSIF (1 Kolom di HP, 2 Kolom di PC) -->
      <form id="form-donor" class="grid grid-cols-1 md:grid-cols-2 gap-6">
        
        <!-- Kolom Nama -->
        <div>
          <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-2">Nama Lengkap</label>
          <input type="text" id="input-nama" required class="w-full px-4 py-3 rounded-xl border border-slate-300 transition-all focus:outline-none focus:border-red-500 focus:ring-4 focus:ring-red-100" placeholder="Contoh: Budi Santoso">
        </div>

        <!-- Kolom NIK -->
        <div>
          <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-2">NIK KTP (16 Digit)</label>
          <input type="text" id="input-nik" required class="w-full px-4 py-3 rounded-xl border border-slate-300 transition-all focus:outline-none focus:border-red-500 focus:ring-4 focus:ring-red-100" placeholder="1871xxxxxxxxxxxx">
        </div>

        <!-- Kolom Golongan Darah -->
        <div>
          <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-2">Golongan Darah</label>
          <select id="input-golongan" class="w-full px-4 py-3 rounded-xl border border-slate-300 transition-all focus:outline-none focus:border-red-500 focus:ring-4 focus:ring-red-100">
            <option value="A">A</option>
            <option value="B">B</option>
            <option value="AB">AB</option>
            <option value="O">O</option>
          </select>
        </div>

        <!-- Kolom Berat Badan -->
        <div>
          <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-2">Berat Badan (Kg)</label>
          <input type="number" id="input-berat" required class="w-full px-4 py-3 rounded-xl border border-slate-300 transition-all focus:outline-none focus:border-red-500 focus:ring-4 focus:ring-red-100" placeholder="Contoh: 65">
        </div>

        <!-- TOMBOL SUBMIT (MEMAKAN 2 KOLOM DI LAYAR LEBAR) -->
        <div class="md:col-span-2 pt-4">
          <button type="submit" class="w-full bg-red-500 hover:bg-red-600 text-white font-bold py-4 rounded-xl shadow-lg shadow-red-100 active:scale-95 transition-all duration-150">
            Kirim Pendaftaran
          </button>
        </div>

      </form>

      <!-- Panel Notifikasi Hasil (Tersembunyi secara default) -->
      <div id="box-notifikasi" class="hidden p-4 rounded-2xl bg-emerald-50 border border-emerald-200 text-emerald-800 text-sm font-medium">
        [Notifikasi akan dimunculkan di sini]
      </div>

    </div>
  </main>

  <!-- FOOTER -->
  <footer class="bg-white border-t border-slate-200 py-6 text-center text-xs text-slate-400">
    <p>&copy; 2026 UDD PMI Provinsi Lampung. Seluruh data dilindungi hak cipta medis.</p>
  </footer>

  <!-- SCRIPT LOGIKA JAVASCRIPT ASINKRON -->
  <script>
    const form = document.getElementById("form-donor");
    const boxNotif = document.getElementById("box-notifikasi");

    form.addEventListener("submit", async (e) => {
        e.preventDefault(); // Mencegah browser merefresh halaman secara otomatis
        
        // 1. Ambil data inputan dari DOM
        const nama = document.getElementById("input-nama").value;
        const nik = document.getElementById("input-nik").value;
        const golDarah = document.getElementById("input-golongan").value;
        const beratBadan = document.getElementById("input-berat").value;

        // 2. Bungkus ke dalam satu objek payload pendaftaran (ES6 Shorthand Property)
        const payload = { nama, nik, golDarah, beratBadan };

        // Tampilkan loading state sementara
        boxNotif.innerText = "⏳ Sedang memproses pendaftaran, harap tunggu...";
        boxNotif.className = "p-4 rounded-2xl bg-amber-50 border border-amber-200 text-amber-800 text-sm font-medium block";

        // 3. Tulis logika Fetch API Asinkron di sini!
        try {
            // Simulasi Delay Tunggu Koneksi 2 detik
            await new Promise(resolve => setTimeout(resolve, 2000));

            // [Lengkapi tantangan E2E asinkron di sini]
            
            // Tampilkan Notifikasi Sukses
            boxNotif.innerText = `✓ Pendaftaran Sukses! Terima kasih ${payload.nama}. Nomor pendaftaran Anda berhasil disimpan di database UDD.`;
            boxNotif.className = "p-4 rounded-2xl bg-emerald-50 border border-emerald-200 text-emerald-800 text-sm font-medium block";
            
            // Reset input form
            form.reset();

        } catch (error) {
            boxNotif.innerText = `🚨 Gagal mengirim data: ${error.message}`;
            boxNotif.className = "p-4 rounded-2xl bg-red-50 border border-red-200 text-red-800 text-sm font-medium block";
        }
    });
  </script>

</body>
</html>
```

---

## 3. Evaluasi & Target Kelulusan
Jika Anda berhasil menyusun kode di atas secara utuh, mencobanya di browser laptop Anda, dan berhasil mengirimkan simulasi payload data registrasi tersebut ke API tanpa memicu error, Anda dinyatakan **LULUS FASE 1** dengan nilai sempurna. 

Anda kini siap melangkah ke **Fase 2** untuk mempelajari framework **Vue 3** & **Nuxt 3**!
```
[FASE 1: SINKRONISASI MATERI FONDASI FRONTEND SUKSES]
```
