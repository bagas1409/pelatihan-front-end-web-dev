# Hari 06: Tailwind Pseudo-classes & States
## Menggunakan Efek Hover, Focus, Active, & Group-Hover pada Tombol Registrasi & Notifikasi CITO

Selamat datang di Hari 06! Hari ini kita akan mempelajari cara menghidupkan interaktivitas halaman web kita menggunakan **Pseudo-classes** di Tailwind CSS.

Sebuah antarmuka premium tidak boleh terasa mati/kaku saat berinteraksi dengan pointer pengguna. Tombol harus berubah warna saat disentuh (*hover*), berubah bayangannya saat diketik (*focus*), mengecil sejenak saat ditekan (*active*), dan bahkan bisa merubah warna elemen anak secara serentak (*group-hover*). Kita akan mengulas cara merakit interaksi mikro ini dengan sangat mudah.

---

## 1. Mengenal Jenis State Pseudo-classes di Tailwind

1.  **`hover:`**: Memicu perubahan visual saat pointer mouse berada di atas elemen (contoh: `hover:bg-red-600`).
2.  **`focus:`**: Memicu perubahan visual saat elemen keyboard sedang memilih kolom input teks tersebut (sangat penting untuk aksesibilitas form tambah relawan). Contoh: `focus:ring-2 focus:ring-red-500`.
3.  **`active:`**: Memicu perubahan visual tepat saat elemen sedang diklik tekan sebelum dilepas (memberikan umpan balik fisik tombol tertekan). Contoh: `active:scale-95`.
4.  **`group-hover:`**: Fitur canggih Tailwind untuk memicu perubahan elemen anak saat elemen pembungkus luarnya (induk) disentuh mouse.

---

## 2. Studi Kasus PMI: Membuat Formulir Input Dinamis & Tombol Interaktif

Kita akan merancang:
1.  **Kolom Input NIK** yang bersinar kemerahan lembut saat diklik petugas admin (`focus:`).
2.  **Tombol CITO Darurat** yang membal mengecil saat ditekan (`active:scale-95`).
3.  **Kartu Agenda Donor** bergaya `group-hover` di mana saat kartu disentuh, ikon teks panah di dalamnya otomatis bergeser ke kanan.

### Contoh Kode HTML Tailwind:

```html
<div class="p-8 bg-slate-50 min-h-screen space-y-6">

  <!-- 1. AREA FORM DENGAN FOCUS STATE -->
  <div class="max-w-md bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
    <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-2">
      Nomor NIK Relawan
    </label>
    <!-- 
      focus:outline-none -> Matikan garis hitam bawaan browser
      focus:border-red-500 -> Ubah garis border menjadi merah saat diketik
      focus:ring-4 focus:ring-red-100 -> Efek pendaran lingkaran merah lembut di luar border
    -->
    <input 
      type="text" 
      placeholder="Contoh: 1871xxxxxxxxxxxx"
      class="w-full px-4 py-3 rounded-xl border border-slate-300 transition-all duration-200 focus:outline-none focus:border-red-500 focus:ring-4 focus:ring-red-100"
    />
  </div>

  <!-- 2. TOMBOL FISIK DENGAN ACTIVE STATE (EFEK MEMBAL / BOUNCE BACK) -->
  <!-- 
    active:scale-95 -> Mengecilkan ukuran tombol menjadi 95% saat diklik (memberikan kepuasan fisik klik)
  -->
  <button class="bg-red-500 hover:bg-red-600 text-white font-bold px-6 py-3 rounded-xl shadow-md shadow-red-200 active:scale-95 transition-transform duration-100">
    Kirim Notifikasi CITO
  </button>

  <!-- 3. KARTU MENU DENGAN GROUP-HOVER (INTERAKSI MULTI-ELEMEN) -->
  <!-- 
    Induk diberi kelas "group"
    Anak diberi kelas "group-hover:translate-x-2" (geser ke kanan 8px saat induk di-hover)
  -->
  <div class="group max-w-sm bg-white p-6 rounded-2xl border border-slate-200 shadow-sm cursor-pointer hover:border-red-300 transition-colors duration-300">
    <h4 class="text-slate-800 font-bold group-hover:text-red-500 transition-colors">
      Detail Lokasi UDD Lampung
    </h4>
    <p class="text-slate-500 text-sm mt-1">Lihat alamat lengkap dan nomor kontak kantor PMI.</p>
    
    <div class="flex items-center text-red-500 font-bold text-xs mt-4">
      <span>Buka Peta</span>
      <!-- Tanda panah otomatis bergeser halus saat kartu disorot -->
      <span class="ml-1 transform transition-transform duration-300 group-hover:translate-x-2">
        &rarr;
      </span>
    </div>
  </div>

</div>
```

---

## 3. Latihan Soal Mandiri
1. Amati kode efek pendaran fokus pada kolom input NIK di atas: `focus:ring-4 focus:ring-red-100`.
2. Jelaskan mengapa pemberian efek pendaran visual (`focus`) sangat penting bagi pengguna yang bernavigasi menggunakan tombol **Tab** di keyboard komputer tanpa bantuan mouse.
3. Rancanglah sebuah kelas tombol kustom Tailwind yang jika ditekan lama (Active) warnanya berubah menjadi abu-abu gelap, bergetar mengecil sejenak, dan bayangan bayangannya menghilang.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya Focus State untuk Aksesibilitas
*   Pengguna dengan keterbatasan motorik seringkali tidak menggunakan mouse komputer, melainkan menekan tombol **Tab** berulang kali di keyboard untuk melompat dari satu tombol ke tombol lain di halaman web.
*   Jika kita mematikan outline bawaan browser (`focus:outline-none`) tanpa memberikan pengganti visual pendaran cincin (`focus:ring-4 focus:ring-red-100`), pengguna keyboard tersebut tidak akan tahu di kolom mana posisi kursor aktif mereka berada (*Invisible Focus Bug*). Ini melanggar hukum aksesibilitas web internasional (WCAG).

### Jawaban 3: Desain Tombol Kustom Active State:
```html
<button class="bg-red-500 text-white font-bold px-6 py-3 rounded-xl shadow-lg hover:bg-red-600 transition-all duration-150 active:bg-slate-800 active:scale-90 active:shadow-none">
  Simpan Data Relawan
</button>
```
*Penjelasan Efek Active*:
*   `active:bg-slate-800`: Warna langsung loncat menjadi abu-abu gelap saat ditekan.
*   `active:scale-90`: Tombol menyusut tajam hingga ukuran 90% (efek pegas dalam).
*   `active:shadow-none`: Bayangan melayang di bawah tombol menghilang seketika, memberi kesan visual tombol telah menyentuh dasar permukaan meja dashboard.
