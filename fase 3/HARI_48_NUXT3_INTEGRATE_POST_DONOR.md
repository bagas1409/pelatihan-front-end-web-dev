# Hari 48: Mengirim Data Form (POST /api/donor)
## Mengirimkan Payload Hasil Registrasi Relawan ke Database Server Backend Menggunakan $fetch

Selamat datang di Hari 48! Hari ini kita akan mempraktikkan proses pengiriman data formulir pendaftaran relawan baru dari aplikasi frontend Nuxt 3 ke database server backend Express.js menggunakan metode HTTP **`POST`**.

Kita akan menyatukan validasi Zod yang sudah dipelajari di Hari 46 & 47 dengan fungsi penembak API **`$fetch`** di dalam fungsi aksi submit guna melakukan pencatatan database yang nyata.

---

## 1. Perbedaan API Request POST vs GET

*   **`GET` (Mengambil Data)**: Kita meminta data dari server (seperti memuat tabel stok darah). Data mengalir dari server turun ke browser client. Kita menggunakan `useFetch` karena aman untuk SSR.
*   **`POST` (Mengirim Data)**: Kita mengirimkan paket data (Payload) baru untuk disimpan ke database server. Data mengalir dari browser client naik ke server. Kita wajib menggunakan **`$fetch`** di dalam event handler karena pengiriman data dipicu oleh tindakan fisik klik tombol simpan dari petugas admin.

---

## 2. Studi Kasus PMI: Registrasi Relawan Terintegrasi ke Backend

Mari kita buat halaman pendaftaran relawan baru di `pages/relawan/baru.vue` yang terhubung langsung ke API server Express.

```html
<!-- pages/relawan/baru.vue -->
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-6">
    <div>
      <h3 class="font-bold text-slate-800 text-base">✍️ Registrasi Relawan Baru</h3>
      <p class="text-xs text-slate-500 mt-1">Kirim data pendaftar baru ke server UDD Lampung</p>
    </div>

    <!-- Alarm Status API -->
    <div v-if="apiResponse" :class="apiResponse.sukses ? 'bg-emerald-50 text-emerald-800' : 'bg-red-50 text-red-800'" class="p-4 rounded-xl text-xs font-semibold">
      {{ apiResponse.pesan }}
    </div>

    <!-- Form Pembungkus Vee-Validate & Zod -->
    <Form :validation-schema="skemaDaftar" @submit="prosesKirimKeBackend" v-slot="{ meta }" class="space-y-4">
      
      <!-- Kolom Nama -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Nama Relawan</label>
        <Field name="nama" type="text" class="w-full px-4 py-2 border rounded-xl outline-none text-sm focus:border-red-500" placeholder="Nama Lengkap" />
        <ErrorMessage name="nama" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- Kolom Golongan Darah -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Golongan Darah</label>
        <Field name="golongan" as="select" class="w-full px-4 py-2 border rounded-xl outline-none text-sm focus:border-red-500">
          <option value="">Pilih Golongan</option>
          <option value="A">A</option>
          <option value="B">B</option>
          <option value="AB">AB</option>
          <option value="O">O</option>
        </Field>
        <ErrorMessage name="golongan" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- Tombol Submit -->
      <button 
        type="submit" 
        :disabled="!meta.valid || loading"
        class="w-full bg-red-500 hover:bg-red-600 disabled:bg-slate-200 text-white font-bold py-3 rounded-xl text-xs transition-colors active:scale-[0.98] transform"
      >
        {{ loading ? '⏳ Mengirim data ke server...' : 'Simpan Data Relawan' }}
      </button>

    </Form>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { Form, Field, ErrorMessage } from 'vee-validate';
import { z } from 'zod';
import { toTypedSchema } from '@vee-validate/zod';

const loading = ref(false);
const apiResponse = ref(null);

// 1. SKEMA VALIDASI FORMULIR
const skemaDaftar = toTypedSchema(
  z.object({
    nama: z.string().min(1, { message: '🚨 Nama wajib diisi!' }).min(3, { message: '🚨 Nama minimal 3 karakter!' }),
    golongan: z.string().min(1, { message: '🚨 Golongan darah wajib dipilih!' })
  })
);

// 2. FUNGSI UNTUK MENGIRIM DATA POST KE BACKEND
const prosesKirimKeBackend = async (values, { resetForm }) => {
  loading.value = true;
  apiResponse.value = null;

  const API_ENDPOINT = 'https://pmi-backend-api.onrender.com/api/volunteers';

  try {
    // Gunakan $fetch asinkron dengan konfigurasi POST method dan body payload
    const respon = await $fetch(API_ENDPOINT, {
      method: 'POST',
      body: {
        nama: values.nama,
        golongan: values.golongan,
        status: 'Aktif'
      }
    });

    apiResponse.value = {
      sukses: true,
      pesan: `✓ Sukses! Relawan "${values.nama}" berhasil disimpan dengan ID: ${respon.id || 'Pusat'}.`
    };

    // Bersihkan formulir kembali kosong jika sukses
    resetForm();

  } catch (err) {
    console.error("Gagal mengirim API POST:", err);
    apiResponse.value = {
      sukses: false,
      pesan: `🚨 Gagal menyimpan data: ${err.data?.message || err.message || 'Koneksi Server Gagal'}`
    };
  } finally {
    loading.value = false;
  }
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati parameter penangkap error pada blok catch: `err.data?.message`.
2. Jelaskan mengapa kita disarankan membaca properti `.data?.message` dari objek error `$fetch` daripada hanya menuliskan `err.message` saja.
3. Sebutkan parameter kedua `{ resetForm }` yang dikirim oleh Vee-Validate pada fungsi submit dan jelaskan kegunaan pemanggilannya sesaat setelah API berhasil diproses.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Membaca Detail Error Response API
*   **`err.message`**: Hanya mengembalikan pesan kesalahan global dari sisi browser (seperti teks string *"Failed to fetch"* atau *"500 Internal Server Error"*). Pesan ini sangat umum dan membingungkan pengguna.
*   **`err.data?.message`**: Mengakses isi pesan error spesifik yang dikirim sengaja oleh server backend Express.js kita (misalnya: *"🚨 Nomor NIK tersebut sudah terdaftar di kota Metro"*). Membaca objek `.data` membantu kita menampilkan pesan error bisnis yang jauh lebih akurat dan ramah kepada petugas admin di layar.

### Jawaban 3: Kegunaan resetForm()
*   **Fungsi `resetForm()`**: Merupakan fungsi utilitas bawaan Vee-Validate.
*   **Kegunaan**: Membersihkan seluruh isi kolom input di layar kembali kosong, sekaligus mereset status penanda error (*dirty* dan *touched*). Jika kita tidak memanggil `resetForm()`, ketikan nama relawan lama akan tetap tertinggal di layar monitor setelah data tersimpan, memicu resiko petugas tidak sengaja mengklik tombol simpan kembali yang menciptakan duplikasi data relawan.
