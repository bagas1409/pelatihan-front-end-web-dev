# Hari 47: Validasi Real-time Kolom Email, Password, & NIK
## Menyediakan Umpan Balik Instan Saat Pengguna Mengetik Untuk Mencegah Kesalahan Input Sejak Dini

Selamat datang di Hari 47! Hari ini kita akan mempraktikkan teknik **Live / Real-time Form Validation** (Validasi Langsung Saat Mengetik) khusus untuk tiga kolom paling krusial pada data relawan: **NIK KTP, Email, dan Password Akun**.

Menunggu pengguna mengklik tombol "Daftar" baru menampilkan pesan error adalah gaya UX yang sudah kuno. Pengguna merasa lebih nyaman jika website langsung memberi tanda ceklis hijau saat mereka selesai mengetik email dengan benar, atau langsung menyalakan tanda silang merah begitu digit NIK KTP yang dimasukkan kurang dari 16 digit.

---

## 1. Konsep Pemicuan Validasi (Validation Triggers)

Vee-Validate menyediakan opsi pemicuan (*Triggers*) kustom yang bisa kita pasang pada komponen `<Form>`:
*   `validateOnInput` $\rightarrow$ Validasi berjalan di setiap ketukan keyboard (*KeyPress*).
*   `validateOnBlur` $\rightarrow$ Validasi berjalan saat kursor pengguna pindah keluar meninggalkan kolom input tersebut (*Focus Out*).
*   `validateOnChange` $\rightarrow$ Validasi berjalan saat nilai kolom mengalami perubahan nilai (*Model Change*).

---

## 2. Studi Kasus PMI: Registrasi Akun Relawan Baru dengan Validasi Live

Mari kita bangun halaman pendaftaran akun relawan DonorKu dengan umpan balik visual Tailwind CSS (border merah jika error, border hijau jika aman).

```html
<!-- pages/relawan/register-live.vue -->
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-6">
    <div>
      <h3 class="font-bold text-slate-800 text-base">👤 Registrasi Akun Relawan</h3>
      <p class="text-xs text-slate-400 mt-1">Umpan balik validasi langsung di layar monitor</p>
    </div>

    <!-- 
      validate-on-input -> Memicu validasi di setiap ketukan keyboard secara real-time
    -->
    <Form 
      :validation-schema="skemaDaftar" 
      @submit="prosesRegister"
      :validate-on-input="true" 
      v-slot="{ errors, meta }"
      class="space-y-4"
    >
      
      <!-- 1. KOLOM NIK KTP -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">NIK KTP (16 Angka)</label>
        <Field 
          name="nik" 
          type="text" 
          placeholder="Masukkan 16 digit NIK" 
          class="w-full px-4 py-2 border rounded-xl outline-none text-sm transition-all focus:ring-4"
          :class="errors.nik 
            ? 'border-red-500 focus:ring-red-100 focus:border-red-500' 
            : 'border-slate-200 focus:ring-emerald-100 focus:border-emerald-500'"
        />
        <ErrorMessage name="nik" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- 2. KOLOM EMAIL -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Alamat Email</label>
        <Field 
          name="email" 
          type="email" 
          placeholder="Contoh: budi@gmail.com" 
          class="w-full px-4 py-2 border rounded-xl outline-none text-sm transition-all focus:ring-4"
          :class="errors.email 
            ? 'border-red-500 focus:ring-red-100' 
            : 'border-slate-200 focus:ring-emerald-100 focus:border-emerald-500'"
        />
        <ErrorMessage name="email" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- 3. KOLOM PASSWORD -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Kata Sandi (Min 8 Karakter)</label>
        <Field 
          name="password" 
          type="password" 
          placeholder="••••••••" 
          class="w-full px-4 py-2 border rounded-xl outline-none text-sm transition-all focus:ring-4"
          :class="errors.password 
            ? 'border-red-500 focus:ring-red-100' 
            : 'border-slate-200 focus:ring-emerald-100 focus:border-emerald-500'"
        />
        <ErrorMessage name="password" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- Tombol Kirim: Otomatis mati jika form belum terisi penuh atau ada error (meta.valid) -->
      <button 
        type="submit" 
        :disabled="!meta.valid"
        class="w-full font-bold py-3 rounded-xl text-xs transition-colors"
        :class="meta.valid 
          ? 'bg-red-500 hover:bg-red-600 text-white cursor-pointer active:scale-95' 
          : 'bg-slate-200 text-slate-400 cursor-not-allowed'"
      >
        Registrasi Sekarang
      </button>

    </Form>
  </div>
</template>

<script setup>
import { Form, Field, ErrorMessage } from 'vee-validate';
import { z } from 'zod';
import { toTypedSchema } from '@vee-validate/zod';

// SKEMA VALIDASI ZOD LIVE
const skemaDaftar = toTypedSchema(
  z.object({
    nik: z.string()
      .min(1, { message: '🚨 NIK wajib diisi!' })
      .regex(/^\d+$/, { message: '🚨 NIK hanya boleh berisi angka!' })
      .length(16, { message: '🚨 NIK harus tepat berjumlah 16 digit!' }),
      
    email: z.string()
      .min(1, { message: '🚨 Email wajib diisi!' })
      .email({ message: '🚨 Format email tidak valid!' }),
      
    password: z.string()
      .min(1, { message: '🚨 Kata sandi wajib diisi!' })
      .min(8, { message: '🚨 Sandi minimal harus memiliki 8 karakter!' })
  })
);

const prosesRegister = (values) => {
  console.log("Registrasi Berhasil!");
  console.log(values);
  alert("Data valid, mengirim payload registrasi relawan...");
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati pembacaan state form menggunakan `v-slot="{ errors, meta }"` di atas.
2. Jelaskan kegunaan properti **`meta.valid`** pada Vee-Validate dan bagaimana kita memanfaatkannya untuk mengunci mati tombol submit dari ancaman klik prematur.
3. Sebutkan apa kelemahan visual dari opsi `validate-on-input="true"` jika dipasang pada kolom input password saat pengguna baru pertama kali mengetikkan huruf pertama. Tuliskan opsi alternatif penanganan UX yang lebih sopan.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Mengenal Slot Props Vee-Validate
*   **Fungsi `meta.valid`**: Merupakan variabel boolean reaktif bawaan Vee-Validate. Dia bernilai `true` **hanya jika seluruh kolom di dalam form sudah lolos pemeriksaan** skema Zod dan tidak memiliki error sama sekali.
*   **Penguncian Tombol**: Dengan menuliskan `:disabled="!meta.valid"`, kita menjamin tombol tidak bisa diklik sama sekali selama data belum lengkap dan benar, menghindarkan server dari request sampah (*Premature submission request*).

### Jawaban 3: Masalah Kepekaan Input & Solusi Blur Validation
*   **Kelemahan Input Real-time**: Jika password minimal harus 8 huruf dan kita mengetik huruf pertama `"a"`, kolom tersebut akan **langsung menyala merah** dan memunculkan error *"🚨 Sandi minimal harus memiliki 8 karakter!"*. Hal ini membuat pengguna merasa disalahkan atau dituduh error padahal mereka memang baru mulai mengetik.
*   **Solusi UX yang Sopan**: Matikan `validate-on-input` dan gunakan pemicu **`validate-on-blur="true"`** (validasi saat blur). Peringatan error hanya akan muncul setelah pengguna memindahkan fokus kursor keluar dari kolom password tersebut (menandakan mereka telah selesai mengetik di kolom tersebut).
