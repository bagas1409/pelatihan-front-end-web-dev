# Hari 62: Validasi Sunting Data dengan Zod Schema
## Mengintegrasikan Validasi Pengaman Zod Pada Formulir Edit dengan Nilai Awal yang Dimuat Dinamis

Selamat datang di Hari 62! Hari ini kita akan menyempurnakan halaman edit data relawan kita di Hari 61 dengan menyematkan sistem **Validasi Zod & Vee-Validate**.

Tantangan terbesar saat menerapkan validasi pada form edit adalah **memasukkan data awal (Initial Values)** yang dinilai lambat karena datang dari API asinkron server. Kita harus memastikan skema Zod tidak berteriak error *"Kolom wajib diisi"* di milidetik pertama halaman dimuat sebelum data dari API berhasil mendarat masuk.

---

## 1. Menyuntikkan Data Awal ke Vee-Validate

Vee-Validate menyediakan properti **`initial-values`** pada komponen `<Form>`. 
*   Kita mengikat properti tersebut ke objek reaktif detail data relawan.
*   Vee-Validate secara pintar akan menunggu (*Wait*) hingga objek tersebut terisi data API, lalu secara otomatis mengisi isian kolom input dan mereset status penilai error.

---

## 2. Studi Kasus PMI: Halaman Edit Relawan Tervalidasi Zod

Mari kita satukan form edit di Hari 61 dengan validasi Zod Schema di file `pages/relawan/edit/[id].vue`:

```html
<!-- pages/relawan/edit/[id].vue -->
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-6">
    <div>
      <h3 class="font-bold text-slate-800 text-base">✏️ Sunting Profil Relawan</h3>
      <p class="text-xs text-slate-400 mt-1">Ubah data dengan kawalan validasi skema Zod</p>
    </div>

    <!-- Tampilan Loading -->
    <div v-if="loadingDetail" class="text-center py-8 text-xs text-slate-400 animate-pulse">
      ⏳ Menjemput data relawan...
    </div>

    <!-- 
      Form Pembungkus:
      :validation-schema -> skema Zod
      :initial-values -> data awal hasil fetch API (formInit)
    -->
    <Form 
      v-else 
      :validation-schema="skemaEdit" 
      :initial-values="formInit"
      @submit="prosesUpdate" 
      v-slot="{ meta }"
      class="space-y-4"
    >
      
      <!-- Kolom Nama -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Nama Relawan</label>
        <Field 
          name="nama" 
          type="text" 
          class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 text-sm" 
          placeholder="Nama Lengkap" 
        />
        <ErrorMessage name="nama" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- Kolom Golongan Darah -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Golongan Darah</label>
        <Field 
          name="golongan" 
          as="select"
          class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 text-sm"
        >
          <option value="A">A</option>
          <option value="B">B</option>
          <option value="AB">AB</option>
          <option value="O">O</option>
        </Field>
        <ErrorMessage name="golongan" class="text-[10px] text-red-500 font-bold block mt-1" />
      </div>

      <!-- Tombol Aksi -->
      <div class="flex gap-3 pt-2 text-xs font-bold">
        <button 
          type="button" 
          @click="batalEdit"
          class="flex-1 py-3 text-slate-500 hover:bg-slate-100 rounded-xl"
        >
          Batal
        </button>
        <button 
          type="submit" 
          :disabled="!meta.valid || loadingSubmit"
          class="flex-1 bg-red-500 hover:bg-red-600 disabled:bg-slate-300 text-white py-3 rounded-xl transition-colors"
        >
          {{ loadingSubmit ? '⌛ Menyimpan...' : 'Simpan Edit' }}
        </button>
      </div>

    </Form>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';
import { Form, Field, ErrorMessage } from 'vee-validate';
import { z } from 'zod';
import { toTypedSchema } from '@vee-validate/zod';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

const route = useRoute();
const id = route.params.id;

const loadingDetail = ref(true);
const loadingSubmit = ref(false);
const { $toast } = useNuxtApp();

// State untuk menampung nilai awal dari API
const formInit = ref({
  nama: '',
  golongan: 'O'
});

// 1. DEFINISI SKEMA EDIT ZOD
const skemaEdit = toTypedSchema(
  z.object({
    nama: z.string()
      .min(1, { message: '🚨 Nama relawan wajib diisi!' })
      .min(3, { message: '🚨 Nama minimal 3 karakter!' }),
    golongan: z.string().min(1, { message: '🚨 Golongan wajib dipilih!' })
  })
);

// 2. AMBIL DATA AWAL DARI API
onMounted(async () => {
  try {
    const data = await $fetch(`https://pmi-backend-api.onrender.com/api/volunteers/${id}`);
    
    // Set nilai awal formInit
    formInit.value = {
      nama: data.nama,
      golongan: data.golongan
    };
  } catch (err) {
    console.error(err);
    $toast.error("🚨 Gagal memuat profil relawan!");
  } finally {
    loadingDetail.value = false;
  }
});

// 3. FUNGSI UPDATE DATA PUT
const prosesUpdate = async (values) => {
  loadingSubmit.value = true;
  try {
    await $fetch(`https://pmi-backend-api.onrender.com/api/volunteers/${id}`, {
      method: 'PUT',
      body: values
    });

    $toast.success("✓ Sukses meng-update data relawan!");
    navigateTo('/relawan');
  } catch (err) {
    console.error(err);
    $toast.error("🚨 Gagal meng-update database!");
  } finally {
    loadingSubmit.value = false;
  }
};

const batalEdit = () => {
  navigateTo('/relawan');
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati atribut `:initial-values="formInit"` pada tag `<Form>` di atas.
2. Jelaskan mengapa kita disarankan menggunakan `initial-values` dari Vee-Validate daripada langsung memasang properti `v-model` dua arah secara kasar pada komponen `<Field>` saat memproses formulir edit yang kompleks.
3. Sebutkan apa kelebihan dari validasi Zod jika data nama yang diketik oleh admin secara tidak sengaja terisi karakter spasi kosong di awal dan akhir ketikan (contoh: `" Budi Santoso "`).

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Keunggulan Initial Values Vee-Validate
*   **Keunggulan Validasi**: Vee-Validate merancang sistem pelacakan error berbasis status murni (*Dirty vs Pristine*). Jika kita memasang `v-model` manual di dalam `<Field>`, Vue akan terus mengubah variabel state di luar siklus pengawasan internal Vee-Validate. 
*   Penggunaan `:initial-values` memastikan Vee-Validate menguasai penuh data awal, mampu mendeteksi apakah pengguna benar-benar mengubah suatu isian (*Dirty State*), dan bisa membandingkan apakah data baru berbeda dengan data lama sebelum tombol simpan di-klik.

### Jawaban 3: Penanganan Spasi Kosong Zod
Zod menyediakan fungsi modifier kustom **`.trim()`** yang terintegrasi di dalam tipe data string:
```javascript
nama: z.string().trim().min(3)
```
*   **Kelebihan**: Jika admin tidak sengaja mengetik spasi kosong di awal/akhir nama, Zod akan memotong (*Trim*) spasi tersebut terlebih dahulu secara otomatis sebelum menghitung jumlah karakter minimum. Hal ini mencegah masuknya nama sampah (hanya spasi kosong) ke database UDD PMI.
