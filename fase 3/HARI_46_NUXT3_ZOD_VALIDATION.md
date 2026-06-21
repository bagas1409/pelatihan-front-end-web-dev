# Hari 46: Integrasi Schema Validation Zod
## Menyusun Skema Validasi Kolom Input yang Ringkas Menggunakan Zod & Vee-Validate

Selamat datang di Hari 46! Hari ini kita akan menyempurnakan sistem validasi formulir kita dengan mengintegrasikan library **Zod** ke dalam **Vee-Validate**.

Di Hari 45 kita terpaksa menulis fungsi JavaScript manual untuk setiap kolom input. Hari ini kita akan beralih ke konsep **Schema Validation** (Validasi Berbasis Skema). Zod memungkinkan kita mendefinisikan satu struktur objek deklaratif (seperti *"Nama harus string minimal 3 huruf, email harus berformat email, NIK harus tepat 16 angka"*) lalu menyuplainya ke Vee-Validate dalam satu baris kode tunggal.

---

## 1. Mengenal Zod & @vee-validate/zod

*   **Zod**: Pustaka deklarasi skema TypeScript/JavaScript yang bertugas memeriksa keabsahan tipe dan format data objek.
*   **`@vee-validate/zod`**: Jembatan konektor yang bertugas menerjemahkan aturan skema Zod menjadi pesan error penunjuk arah yang dipahami oleh komponen `<Form>` Vee-Validate.

---

## 2. Langkah Instalasi Library

Jalankan perintah penginstalan berikut di terminal root proyek Nuxt Anda:

```bash
npm install zod @vee-validate/zod
```

---

## 3. Studi Kasus PMI: Skema Validasi Tambah Jadwal Donor Keliling

Mari kita buat halaman formulir pendaftaran jadwal donor keliling (Agenda Donor) yang divalidasi penuh menggunakan Zod Schema.

### Berkas Halaman: `pages/event/baru.vue`

```html
<!-- pages/event/baru.vue -->
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-6">
    <div>
      <h3 class="font-bold text-slate-800 text-base">📅 Tambah Agenda Donor</h3>
      <p class="text-xs text-slate-400 mt-1">Buat jadwal agenda donor darah keliling UDD</p>
    </div>

    <!-- 
      :validation-schema -> Menyambungkan form dengan skema Zod kita
    -->
    <Form :validation-schema="skemaAgenda" @submit="prosesSimpan" class="space-y-4">
      
      <!-- 1. KOLOM LOKASI -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Nama Lokasi Event</label>
        <Field name="lokasi" type="text" class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 text-sm" placeholder="Contoh: Mall Bumi Kedaton" />
        <ErrorMessage name="lokasi" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- 2. KOLOM TARGET KANTONG -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Target Persediaan (Kantong)</label>
        <Field name="targetKantong" type="number" class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 text-sm" placeholder="Contoh: 100" />
        <ErrorMessage name="targetKantong" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- 3. KOLOM EMAIL PENANGGUNG JAWAB -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Email Koordinator</label>
        <Field name="emailKoordinator" type="email" class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 text-sm" placeholder="koordinator@pmi.org" />
        <ErrorMessage name="emailKoordinator" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- Tombol Kirim -->
      <button 
        type="submit" 
        class="w-full bg-red-500 hover:bg-red-600 text-white font-bold py-3 rounded-xl text-xs transition-colors"
      >
        Simpan Agenda
      </button>

    </Form>
  </div>
</template>

<script setup>
import { Form, Field, ErrorMessage } from 'vee-validate';
import { z } from 'zod';
import { toTypedSchema } from '@vee-validate/zod';

// DEFINISIKAN SKEMA VALIDASI MENGGUNAKAN ZOD
const skemaAgenda = toTypedSchema(
  z.object({
    
    // Lokasi wajib berupa teks string minimal 5 huruf
    lokasi: z.string()
      .min(1, { message: '🚨 Lokasi agenda wajib diisi!' })
      .min(5, { message: '🚨 Nama lokasi minimal harus 5 karakter!' }),
      
    // Target kantong harus berupa angka, bernilai minimal 10 kantong
    targetKantong: z.preprocess(
      (val) => Number(val), // Ubah input string dari HTML menjadi angka asli
      z.number({ invalid_type_error: '🚨 Wajib berupa angka!' })
       .min(10, { message: '🚨 Target donor minimal adalah 10 kantong!' })
    ),
    
    // Email koordinator harus berformat alamat email yang sah
    emailKoordinator: z.string()
      .min(1, { message: '🚨 Email koordinator wajib diisi!' })
      .email({ message: '🚨 Format alamat email tidak valid!' })

  })
);

const prosesSimpan = (values) => {
  console.log("Data Lolos Validasi Zod!");
  console.log(values);
  alert("Sukses menyimpan agenda baru!");
};
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati kode pendefinisian skema Zod di atas: `targetKantong: z.preprocess(...)`.
2. Jelaskan fungsi dari perintah helper `z.preprocess()` pada skema di atas dan mengapa kita harus menggunakannya untuk kolom input bertipe angka pada formulir HTML.
3. Sebutkan kelebihan dari penggunaan library Zod (dibandingkan mendefinisikan rules manual di browser) jika sewaktu-waktu tim backend meminta skema validasi yang sama untuk dipasang di server Node.js.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan Preprocess Zod
*   **Fungsi `z.preprocess()`**: Melakukan transformasi/konversi tipe data awal sebelum data tersebut masuk ke tahap validasi utama Zod.
*   **Alasan Penggunaan**: Secara default, tag HTML `<input type="number">` tetap mengirimkan datanya ke JavaScript dalam tipe data **String** (contoh: `"100"`). Jika kita langsung memvalidasinya dengan `z.number()`, Zod akan menolak dan mengeluarkan error *"Expected number, got string"*. `z.preprocess()` bertugas mengubah string `"100"` menjadi angka integer `100` terlebih dahulu agar bisa divalidasi dengan aman oleh rules `.min(10)`.

### Jawaban 3: Keunggulan Portabilitas Kode Zod (Isomorphic Validation)
*   **Berbagi Skema Kode (Code Sharing)**: Zod bersifat universal (bisa dijalankan di browser maupun di runtime Node.js backend). 
*   Kita bisa mengekspor objek skema `skemaAgenda` tersebut ke dalam satu file berkas terpisah, lalu membagikannya ke tim backend Express.js. Backend bisa langsung menggunakannya untuk validasi request API: `skemaAgenda.parse(req.body)`. Hal ini menjamin aturan validasi di sisi user (frontend) dan sisi database (backend) 100% konsisten tanpa perlu menulis ulang baris logika baru.
