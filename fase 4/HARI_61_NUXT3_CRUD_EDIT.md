# Hari 61: CRUD Relawan - Fitur Edit & Auto-Fill Form
## Merancang Halaman Sunting Data Menggunakan Metode HTTP PUT & auto-fill Form Dari Detail API

Selamat datang di Hari 61! Hari ini kita akan mempelajari cara membangun alur **Edit / Update Data (Penyuntingan)** pada operasi CRUD database relawan PMI.

Operasi edit data memiliki tantangan alur kerja yang unik: saat halaman edit dibuka, kolom-kolom formulir tidak boleh kosong melompong. Kita harus mengambil data detail relawan yang bersangkutan dari server terlebih dahulu (`GET /api/volunteers/:id`), memasukkannya ke dalam isian form secara otomatis (**Auto-Fill**), membiarkan petugas mengedit data, lalu mengirimkan payload data baru tersebut kembali ke server menggunakan metode HTTP **`PUT`** atau **`PATCH`**.

---

## 1. Aliran Kerja Operasi Edit/Update

```
1. Klik Tombol Edit di Tabel -> 2. Navigasi ke /relawan/edit/[id] -> 3. Panggil API GET /api/volunteers/[id] -> 4. Auto-fill isi Form -> 5. Sunting data -> 6. Klik Simpan -> 7. Tembak API PUT /api/volunteers/[id] -> 8. Redirect kembali ke Daftar Relawan
```

---

## 2. Studi Kasus PMI: Halaman Sunting Data Relawan UDD

Mari kita buat halaman edit dinamis menggunakan struktur berkas folder bersarang **`pages/relawan/edit/[id].vue`**.

### Langkah 1: Buat Berkas `pages/relawan/edit/[id].vue`
Buat struktur folder jika belum ada, lalu ciptakan berkas **`[id].vue`**:

```html
<!-- pages/relawan/edit/[id].vue -->
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-6">
    <div>
      <h3 class="font-bold text-slate-800 text-base">✏️ Sunting Profil Relawan</h3>
      <p class="text-xs text-slate-400 mt-1">Ubah data identitas relawan terdaftar</p>
    </div>

    <!-- Tampilan Loading jika data detail sedang dijemput dari server -->
    <div v-if="loadingDetail" class="text-center py-8 text-xs text-slate-400 animate-pulse">
      ⏳ Mengambil detail data relawan dari database...
    </div>

    <!-- Tampilan Form Utama -->
    <form v-else @submit.prevent="prosesSimpanPerubahan" class="space-y-4">
      
      <!-- Kolom Nama (Diikat ke form.nama) -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Nama Relawan</label>
        <input 
          type="text" 
          v-model="form.nama" 
          required
          class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 text-sm"
          placeholder="Nama Lengkap" 
        />
      </div>

      <!-- Kolom Golongan Darah (Diikat ke form.golongan) -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Golongan Darah</label>
        <select 
          v-model="form.golongan" 
          required
          class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 text-sm"
        >
          <option value="A">A</option>
          <option value="B">B</option>
          <option value="AB">AB</option>
          <option value="O">O</option>
        </select>
      </div>

      <!-- Tombol Aksi -->
      <div class="flex gap-3 pt-2 text-xs font-bold">
        <button 
          type="button"
          @click="kembaliKeDaftar"
          class="flex-1 py-3 text-slate-500 hover:bg-slate-100 rounded-xl transition-colors"
        >
          Batal
        </button>
        <button 
          type="submit" 
          :disabled="loadingSubmit"
          class="flex-1 bg-red-500 hover:bg-red-600 disabled:bg-slate-300 text-white py-3 rounded-xl transition-colors"
        >
          {{ loadingSubmit ? '⏳ Menyimpan...' : 'Simpan Perubahan' }}
        </button>
      </div>

    </form>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

const route = useRoute();
const idRelawan = route.params.id; // Ambil ID dari URL params edit

const loadingDetail = ref(true);
const loadingSubmit = ref(false);

const { $toast } = useNuxtApp(); // Notifikasi melayang

// Tempat menampung data isian form
const form = ref({
  nama: '',
  golongan: 'O'
});

// 1. LIFECYCLE MOUNTED: Ambil detail data relawan saat halaman pertama kali dibuka
onMounted(async () => {
  const API_DETAIL_URL = `https://pmi-backend-api.onrender.com/api/volunteers/${idRelawan}`;
  
  try {
    const data = await $fetch(API_DETAIL_URL);
    
    // AUTO-FILL: Isi data form reaktif dengan data balikan dari API server
    form.value.nama = data.nama;
    form.value.golongan = data.golongan;
    
  } catch (err) {
    console.error("Gagal menjemput data detail:", err);
    $toast.error("🚨 Gagal memuat profil relawan!");
  } finally {
    loadingDetail.value = false;
  }
});

// 2. FUNGSI SUBMIT: Tembak API PUT untuk meng-update database
const prosesSimpanPerubahan = async () => {
  loadingSubmit.value = true;
  const API_UPDATE_URL = `https://pmi-backend-api.onrender.com/api/volunteers/${idRelawan}`;

  try {
    await $fetch(API_UPDATE_URL, {
      method: 'PUT', // Menggunakan metode PUT untuk update data penuh
      body: {
        nama: form.value.nama,
        golongan: form.value.golongan
      }
    });

    $toast.success("✓ Profil relawan berhasil diperbarui!");
    
    // Kembalikan ke halaman daftar utama setelah sukses
    await navigateTo('/relawan');

  } catch (err) {
    console.error("Gagal meng-update data:", err);
    $toast.error(`🚨 Gagal memperbarui: ${err.data?.message || err.message}`);
  } finally {
    loadingSubmit.value = false;
  }
};

const kembaliKeDaftar = () => {
  navigateTo('/relawan');
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati alur penulisan asinkron pada fungsi `onMounted` di atas.
2. Jelaskan mengapa kita harus menyetel status `loadingDetail.value = true` di awal fungsi mount, dan mengembalikannya menjadi `false` pada blok `finally` di akhir pencarian data.
3. Sebutkan perbedaan fungsi kegunaan metode HTTP **`PUT`** dengan metode HTTP **`PATCH`** dalam proses manipulasi data di database server.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pengaturan Status Loading Detail
*   **Alasan Pengaturan**: Memanggil API server membutuhkan waktu tunggu (sekitar 0.5 - 2 detik). Selama proses tunggu asinkron tersebut berjalan di internet, data variabel `form.nama` masih bernilai kosong.
*   Jika kita tidak menyembunyikan form di balik status `loadingDetail` (`v-if`), form kosong akan **sempat menyala kedip kosong** di layar monitor sebelum mendadak terisi teks. Ini merusak kenyamanan visual (*Layout Flickering*). Menampilkan loader di awal dan mematikannya di blok `finally` memastikan form hanya digambar setelah data siap di-fill.

### Jawaban 3: Perbedaan PUT vs PATCH
1.  **`PUT` (Update Menyeluruh - Replace)**: Kita mengirimkan seluruh data objek secara lengkap ke server. Server akan menimpa seluruh kolom lama di database dengan data baru kiriman kita. Jika ada kolom lama yang lupa kita kirimkan, kolom tersebut rawan terhapus atau bernilai null di database.
2.  **`PATCH` (Update Sebagian - Modify)**: Kita hanya mengirimkan kolom data spesifik yang diubah saja (misalnya hanya mengirimkan `{ golongan: 'AB' }` karena kolom nama tidak diganti). Server hanya akan menyunting kolom golongan darah tersebut dan membiarkan kolom nama lama tetap aman terisi di database, sangat hemat transfer bandwidth internet.
