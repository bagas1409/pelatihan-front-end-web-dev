# Hari 45: Validasi Formulir di Nuxt 3
## Menggunakan Library Vee-Validate untuk Mencegah Pengiriman Formulir Kosong

Selamat datang di Hari 45! Hari ini kita akan mempelajari cara mengelola **Validasi Formulir** (Form Validation) menggunakan library standar industri Vue 3: **Vee-Validate**.

Saat petugas UDD menginput data pendaftaran relawan donor darah baru, kita harus membatasi ketikan agar tidak asal-asalan (seperti: NIK KTP wajib diisi tepat 16 digit angka, kolom nama tidak boleh kosong, dan email harus berformat alamat email valid). Melakukan pengecekan `if-else` secara manual untuk belasan kolom formulir akan menghasilkan baris kode Javascript yang sangat panjang dan kotor. Kita akan belajar cara merapikannya secara deklaratif.

---

## 1. Mengapa Menggunakan Vee-Validate?

Vee-Validate adalah pustaka khusus yang mengurus manajemen formulir di Vue 3 dengan keunggulan:
1.  **Melacak State Kolom Form**: Dia mendeteksi secara otomatis apakah suatu kolom sudah pernah disentuh pengguna (*dirty*), sedang dalam proses diketik (*touch*), atau bernilai valid (*valid*).
2.  **Pemberitahuan Error Instan**: Menampilkan pesan peringatan merah tepat di bawah kolom yang bersangkutan sesaat setelah pengguna mengetik salah data.
3.  **Mencegah Submit**: Tombol kirim form secara otomatis akan terkunci mati selama masih ada kolom input yang tidak memenuhi syarat validasi.

---

## 2. Langkah Instalasi Vee-Validate

Jalankan perintah penginstalan library inti Vee-Validate di terminal proyek Nuxt Anda:

```bash
npm install vee-validate
```

---

## 3. Studi Kasus PMI: Membuat Form Registrasi Anggota Relawan

Mari kita buat halaman formulir pendaftaran relawan baru dengan validasi rules dasar menggunakan komponen `<Form>` dan `<Field>` bawaan Vee-Validate.

### Berkas Halaman: `pages/relawan/baru.vue`

```html
<!-- pages/relawan/baru.vue -->
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-6">
    <div>
      <h3 class="font-bold text-slate-800 text-base">✍️ Registrasi Relawan Baru</h3>
      <p class="text-xs text-slate-400 mt-1">Pastikan seluruh data KTP diinput dengan benar</p>
    </div>

    <!-- 
      Vee-Validate Form Pembungkus:
      @submit -> Hanya akan terpicu jika seluruh validasi di dalam Field bernilai sukses
    -->
    <Form @submit="prosesDaftar" class="space-y-4">
      
      <!-- 1. FIELD NAMA LENGKAP -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Nama Relawan</label>
        <!-- :rules="valNama" menghubungkan input dengan fungsi pemeriksa JS -->
        <Field 
          name="nama" 
          type="text" 
          :rules="valNama"
          placeholder="Nama Sesuai KTP" 
          class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 text-sm"
        />
        <!-- ErrorMessage bertugas merender pesan error jika rule gagal -->
        <ErrorMessage name="nama" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- 2. FIELD NIK KTP -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">NIK KTP (16 Digit)</label>
        <Field 
          name="nik" 
          type="text" 
          :rules="valNik"
          placeholder="187105xxxxxxxxxx" 
          class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 text-sm"
        />
        <ErrorMessage name="nik" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- Tombol Kirim -->
      <button 
        type="submit" 
        class="w-full bg-red-500 hover:bg-red-600 text-white font-bold py-3 rounded-xl text-xs transition-colors active:scale-95 transform"
      >
        Daftarkan Relawan
      </button>

    </Form>
  </div>
</template>

<script setup>
import { Form, Field, ErrorMessage } from 'vee-validate';

// 1. ATURAN VALIDASI UNTUK NAMA
const valNama = (nilai) => {
  if (!nilai) {
    return '🚨 Kolom Nama Lengkap wajib diisi!';
  }
  if (nilai.length < 3) {
    return '🚨 Nama minimal harus memiliki 3 karakter!';
  }
  return true; // Kembalikan true jika lolos pemeriksaan
};

// 2. ATURAN VALIDASI UNTUK NIK
const valNik = (nilai) => {
  if (!nilai) {
    return '🚨 Nomor NIK KTP wajib diisi!';
  }
  // Cek apakah input berisi karakter non-angka
  const hanyaAngka = /^\d+$/;
  if (!hanyaAngka.test(nilai)) {
    return '🚨 NIK hanya boleh berisi karakter angka saja!';
  }
  if (nilai.length !== 16) {
    return `🚨 NIK harus tepat berjumlah 16 digit! (Input saat ini: ${nilai.length} digit)`;
  }
  return true;
};

// 3. FUNGSI SUBMIT JIKA VALID
const prosesDaftar = (values) => {
  console.log("Form dinyatakan Valid! Mengirim data ke server backend...");
  console.log(values); // values berisi objek { nama: '...', nik: '...' }
  alert(`Sukses! Relawan "${values.nama}" siap didaftarkan.`);
};
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati bagaimana fungsi penanganan error pada inputan KTP bekerja di atas.
2. Jelaskan fungsi dari rumus regex **`/^\d+$/`** yang digunakan pada fungsi validator `valNik` di atas dan apa akibatnya bagi inputan NIK jika kita mengetikkan karakter huruf di dalam kolom input tersebut.
3. Sebutkan kelemahan menuliskan skema validasi manual menggunakan fungsi-fungsi JavaScript individual (seperti `valNama`, `valNik`) jika formulir kita memiliki 50 kolom input yang tebal.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Regex Validation
*   **Fungsi `/^\d+$/`**: Adalah ekspresi reguler (Regular Expression / Regex) di mana `^` menandakan awal string, `\d+` mendeteksi karakter angka (digit) berjumlah 1 atau lebih, dan `$` menandakan akhir string. Rumus ini memastikan seluruh string **hanya boleh diisi angka 0-9**.
*   **Akibat jika salah ketik**: Jika petugas mengetikkan huruf (misal: `"1871a2"`) di kolom input, metode `.test(nilai)` akan mendeteksi keberadaan karakter non-angka, bernilai `false`, dan langsung memicu return pesan error *"🚨 NIK hanya boleh berisi karakter angka saja!"* di bawah kolom tanpa memproses submit form.

### Jawaban 3: Kelemahan Fungsi Validator Individual
*   **Kelemahan**: Menuliskan fungsi validator individual untuk 50 kolom akan membuat baris kode di script setup Anda menjadi sangat panjang (ratusan baris hanya untuk fungsi penanganan string). 
*   **Solusi Modern**: Di Hari 46, kita akan menggabungkan Vee-Validate dengan library **Zod / Yup** (Schema Validation). Kita cukup mendefinisikan 1 objek skema validasi terpusat yang ringkas untuk 50 kolom sekaligus secara bersih.
