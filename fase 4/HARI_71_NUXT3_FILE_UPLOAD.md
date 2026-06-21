# Hari 71: Integrasi Mengunggah File Gambar (File Upload)
## Mengunggah Foto Profil Relawan Ke Server Backend Menggunakan FormData Object & Input File

Selamat datang di Hari 71! Hari ini kita akan mempelajari cara membangun fitur **Unggah Gambar** (File/Image Upload) menggunakan JavaScript **`FormData`** di Nuxt 3.

Pada portal UDD PMI, petugas pendaftaran harus mengunggah foto KTP atau pas foto profil relawan untuk keperluan verifikasi fisik. Mengirimkan berkas file biner (seperti gambar `.png` atau `.jpg`) tidak bisa dilakukan menggunakan objek JSON biasa. Kita harus membungkus file tersebut ke dalam kontainer khusus bernama `FormData` agar browser memahami cara mengirimkan berkas digital tersebut lewat kabel internet.

---

## 1. Mengenal FormData & Konfigurasi Header Content-Type

*   **`FormData`**: Objek bawaan JavaScript yang mensimulasikan data formulir HTML berformat enkripsi **`multipart/form-data`**.
*   **Peringatan Otoritas Header**: Saat mengirimkan JSON biasa, kita menyuplai `'Content-Type': 'application/json'`. Namun, saat mengirimkan `FormData`, kita **tidak boleh menuliskan Content-Type manual**! Kita wajib membiarkan browser yang menghitung batas pembatas boundary file secara otomatis agar server backend tidak gagal membaca berkas yang diunggah.

---

## 2. Studi Kasus PMI: Formulir Unggah Foto Profil Relawan

Mari kita buat halaman khusus untuk memperbarui foto profil relawan di `pages/relawan/upload-foto.vue`.

```html
<!-- pages/relawan/upload-foto.vue -->
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-6">
    <div>
      <h3 class="font-bold text-slate-800 text-base">📸 Unggah Foto Profil Relawan</h3>
      <p class="text-xs text-slate-400 mt-1">Format yang diizinkan: JPG, JPEG, atau PNG (Maks 2MB)</p>
    </div>

    <form @submit.prevent="prosesUnggahFoto" class="space-y-4">
      
      <!-- Input File Tersembunyi, Digantikan Oleh Desain Box Kustom -->
      <div 
        @click="pilihFile"
        class="border-2 border-dashed border-slate-200 hover:border-red-500 rounded-3xl p-8 text-center cursor-pointer transition-colors bg-slate-50/50 hover:bg-slate-50"
      >
        <input 
          type="file" 
          ref="fileInputRef" 
          @change="tanganiFileMasuk" 
          accept="image/*"
          class="hidden" 
        />
        
        <!-- Pratinjau Gambar di Layar sebelum diunggah -->
        <div v-if="previewUrl" class="space-y-2">
          <img :src="previewUrl" class="mx-auto w-32 h-32 object-cover rounded-full border shadow-inner" />
          <p class="text-xs font-bold text-slate-500">{{ namaFile }}</p>
        </div>

        <div v-else class="space-y-2 text-slate-400">
          <span class="text-3xl block">📤</span>
          <p class="text-xs font-semibold">Klik untuk memilih foto dari penyimpanan galeri HP</p>
        </div>
      </div>

      <!-- Tombol Aksi Unggah -->
      <button 
        type="submit" 
        :disabled="!selectedFile || loading"
        class="w-full bg-red-500 hover:bg-red-600 disabled:bg-slate-200 text-white font-bold py-3 rounded-xl text-xs transition-colors"
      >
        {{ loading ? '⏳ Mengunggah foto...' : 'Mulai Unggah Foto' }}
      </button>

    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

const fileInputRef = ref(null);
const selectedFile = ref(null);
const previewUrl = ref(null);
const namaFile = ref('');
const loading = ref(false);

const { $toast } = useNuxtApp();

// 1. Membuka jendela dialog pemilih file
const pilihFile = () => {
  fileInputRef.value.click();
};

// 2. Menangkap file yang dipilih user
const tanganiFileMasuk = (event) => {
  const file = event.target.files[0];
  if (!file) return;

  // A. Batasi ukuran file (Maksimal 2MB = 2 * 1024 * 1024 bytes)
  if (file.size > 2 * 1024 * 1024) {
    $toast.error("🚨 File terlalu besar! Maksimal ukuran file adalah 2MB.");
    return;
  }

  selectedFile.value = file;
  namaFile.value = file.name;

  // B. Membuat URL pratinjau gambar instan di browser
  previewUrl.value = URL.createObjectURL(file);
};

// 3. Mengirimkan file via FormData POST
const prosesUnggahFoto = async () => {
  if (!selectedFile.value) return;

  loading.value = true;
  
  // Bungkus file biner ke objek FormData
  const payload = new FormData();
  payload.append('avatar', selectedFile.value); // 'avatar' adalah key field yang dibaca backend Express

  const API_UPLOAD_URL = 'https://pmi-backend-api.onrender.com/api/users/profile'; // Contoh endpoint upload

  try {
    const respon = await $fetch(API_UPLOAD_URL, {
      method: 'POST',
      body: payload
      // PENTING: Dilarang menuliskan headers: { 'Content-Type': 'multipart/form-data' }!
      // Biarkan $fetch yang menentukan boundary secara otomatis.
    });

    $toast.success("✓ Foto profil relawan berhasil diperbarui di server!");
    
    // Reset form kembali kosong setelah sukses
    selectedFile.value = null;
    previewUrl.value = null;

  } catch (err) {
    console.error("Gagal mengunggah file:", err);
    $toast.error(`🚨 Upload Gagal: ${err.data?.message || err.message}`);
  } finally {
    loading.value = false;
  }
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati kode pratinjau gambar: `URL.createObjectURL(file)`.
2. Jelaskan fungsi dari pemanggilan metode `URL.createObjectURL()` di atas dan apa keuntungannya bagi kecepatan rendering pratinjau visual sebelum file benar-benar meluncur terunggah ke database server internet.
3. Sebutkan mengapa kita dilarang keras menuliskan header `'Content-Type': 'multipart/form-data'` secara manual saat mengirimkan objek `FormData` melalui helper `$fetch`.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan URL.createObjectURL()
*   **Fungsi `URL.createObjectURL()`**: Membuat alamat URL lokal tiruan di dalam memori browser (contoh: `blob:http://localhost:3000/a7b1-4c22...`) yang menunjuk langsung ke berkas biner di dalam RAM komputer user.
*   **Keuntungannya**: Browser bisa memuat pratinjau gambar tersebut secara instan dalam waktu **0 milidetik** tanpa perlu membuang kuota internet untuk mengunggah gambar tersebut terlebih dahulu ke server cloud. Petugas bisa mengevaluasi apakah foto yang dipilih ngeblur/salah sebelum memutuskan mengklik tombol kirim.

### Jawaban 3: Larangan Set Content-Type Manual pada FormData
*   **Masalah Boundary**: Browser mengirimkan berkas `multipart` dengan membagi file ke dalam potongan-potongan kecil yang dipisahkan oleh kode pembatas acak unik bernama **Boundary** (contoh: `------WebKitFormBoundary7MA4YWxkTrZu0gW`).
*   Jika kita memaksa menulis `'Content-Type': 'multipart/form-data'` secara manual di header, browser akan menghapus kode boundary acak tersebut. Akibatnya, server backend Express.js Anda akan kebingungan membaca data yang datang karena tidak tahu di mana awal dan akhir potongan file biner tersebut, memicu error crash backend *"Multipart: Boundary not found"*.
