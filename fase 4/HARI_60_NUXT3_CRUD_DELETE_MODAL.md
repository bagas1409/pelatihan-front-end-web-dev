# Hari 60: CRUD Relawan - Fitur Hapus & Modal Konfirmasi
## Menambahkan Aksi Hapus Relawan Menggunakan Metode HTTP DELETE Lengkap Dengan Teleport Modal

Selamat datang di Hari 60! Hari ini kita akan menyelesaikan salah satu fitur krusial dari operasi CRUD: **Delete (Penghapusan Data)** dengan menyertakan dialog konfirmasi pengaman melayang.

Menyediakan aksi hapus data secara langsung tanpa bertanya konfirmasi adalah dosa UX yang sangat fatal. Petugas admin bisa saja tidak sengaja mengklik tombol hapus saat sedang menatap layar HP, yang berakibat terhapusnya data relawan secara permanen dari database. Kita akan merakit tombol hapus yang dikawal oleh modal konfirmasi di bawah perlindungan tag `<Teleport>` agar aman dari tumpang tindih layout.

---

## 1. Konsep Penghapusan Data HTTP DELETE

*   **Endpoint API**: Sesuai dengan REST API standar, aksi hapus menargetkan endpoint `/api/volunteers/:id` menggunakan metode HTTP **`DELETE`**.
*   **Aliran Kerja**:
    1.  Petugas mengklik ikon sampah merah di tabel relawan.
    2.  Variabel reaktif `relawanTerpilih` mencatat objek ID relawan tersebut, dan status `showModal` bernilai `true`.
    3.  Modal dialog melayang muncul di layar (diteleportasikan ke `<body>`).
    4.  Jika petugas mengklik tombol "Ya, Hapus Data", frontend menembak API `$fetch(..., { method: 'DELETE' })` ke server.
    5.  Data di database terhapus, modal menutup, dan tabel relawan menyegarkan diri (*refresh*).

---

## 2. Studi Kasus PMI: Menambahkan Tombol Hapus pada Tabel Relawan

Buka kembali berkas halaman **`pages/relawan/index.vue`** Anda, lalu mari kita edit isinya untuk menggabungkan fungsi hapus data.

```html
<!-- pages/relawan/index.vue (Versi Lengkap dengan Hapus & Modal) -->
<template>
  <div class="space-y-6">
    
    <!-- Header Halaman -->
    <div class="flex justify-between items-center border-b pb-4">
      <h2 class="text-xl font-bold text-slate-800">👥 Database Relawan PMI</h2>
      <NuxtLink to="/relawan/baru" class="bg-red-500 text-white font-bold text-xs px-4 py-2.5 rounded-xl">
        + Registrasi Baru
      </NuxtLink>
    </div>

    <!-- Tabel Data -->
    <div class="bg-white rounded-3xl border border-slate-200 shadow-sm overflow-hidden">
      <table class="w-full text-left border-collapse">
        <thead class="bg-slate-50 border-b text-xs font-bold text-slate-500 uppercase">
          <tr>
            <th class="py-4 px-6">Nama Relawan</th>
            <th class="py-4 px-6">Golongan Darah</th>
            <th class="py-4 px-6 text-right">Aksi</th>
          </tr>
        </thead>

        <tbody class="text-sm divide-y divide-slate-100">
          <tr v-for="relawan in responApi?.data" :key="relawan.id" class="hover:bg-slate-50/50">
            <td class="py-4 px-6 font-bold text-slate-800">{{ relawan.nama }}</td>
            <td class="py-4 px-6 font-bold text-red-500">{{ relawan.golongan }}</td>
            <td class="py-4 px-6 text-right space-x-3">
              <NuxtLink :to="`/relawan/${relawan.id}`" class="text-xs text-blue-500 font-bold hover:underline">Detail</NuxtLink>
              
              <!-- Tombol Hapus dengan mengirimkan parameter ID relawan -->
              <button 
                @click="konfirmasiHapus(relawan)" 
                class="text-xs text-red-500 font-bold hover:underline"
              >
                Hapus
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- TELEPORT DIALOG MODAL KONFIRMASI (Ke Root Body) -->
    <Teleport to="body">
      <div 
        v-if="showModal" 
        class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm flex items-center justify-center p-4 z-[9999]"
      >
        <div class="bg-white p-6 rounded-3xl max-w-sm w-full space-y-4 shadow-xl border border-slate-100">
          <div class="text-red-500 text-2xl">⚠️</div>
          <h3 class="font-bold text-slate-900 text-base">Hapus Data Relawan?</h3>
          <p class="text-xs text-slate-500 leading-relaxed">
            Apakah Anda yakin ingin menghapus data atas nama <strong>{{ relawanTerpilih?.nama }}</strong>? 
            Tindakan ini bersifat permanen dan tidak bisa dibatalkan.
          </p>
          
          <div class="flex justify-end gap-2 pt-2 text-xs">
            <button 
              @click="showModal = false" 
              :disabled="prosesHapusLoading"
              class="px-4 py-2 font-bold text-slate-500 hover:bg-slate-100 rounded-xl"
            >
              Batal
            </button>
            <button 
              @click="eksekusiHapusData" 
              :disabled="prosesHapusLoading"
              class="px-4 py-2 font-bold text-white bg-red-500 hover:bg-red-600 rounded-xl disabled:bg-slate-300"
            >
              {{ prosesHapusLoading ? '⌛ Menghapus...' : 'Ya, Hapus Data' }}
            </button>
          </div>
        </div>
      </div>
    </Teleport>

  </div>
</template>

<script setup>
import { ref } from 'vue';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

// Load data dengan useApi
const { data: responApi, refresh } = await useApi('/volunteers');

const showModal = ref(false);
const relawanTerpilih = ref(null);
const prosesHapusLoading = ref(false);

const { $toast } = useNuxtApp(); // Import toast global

// 1. Pemicu munculnya dialog modal konfirmasi
const konfirmasiHapus = (relawan) => {
  relawanTerpilih.value = relawan;
  showModal.value = true;
};

// 2. Fungsi eksekutor menembak API DELETE ke backend
const eksekusiHapusData = async () => {
  if (!relawanTerpilih.value) return;

  prosesHapusLoading.value = true;
  const API_DELETE_URL = `https://pmi-backend-api.onrender.com/api/volunteers/${relawanTerpilih.value.id}`;

  try {
    // Tembak API DELETE menggunakan $fetch asinkron
    await $fetch(API_DELETE_URL, {
      method: 'DELETE'
    });

    // Tampilkan Toast Sukses
    $toast.success(`✓ Data ${relawanTerpilih.value.nama} berhasil dihapus dari sistem UDD!`);
    
    // Tutup Modal
    showModal.value = false;

    // Segarkan isi tabel relawan
    refresh();

  } catch (err) {
    console.error("Gagal menghapus data:", err);
    $toast.error(`🚨 Gagal: ${err.data?.message || err.message}`);
  } finally {
    prosesHapusLoading.value = false;
  }
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati alur penutupan modal di atas: `showModal.value = false` dan pemanggilan `refresh()`.
2. Jelaskan mengapa kita harus memanggil fungsi `refresh()` setelah API DELETE berhasil diproses oleh server backend dan apa hubungannya dengan sinkronisasi database visual di layar.
3. Sebutkan apa kelemahan jika kita meletakkan tag div modal dialog melayang langsung di samping baris tombol hapus tabel tanpa dibungkus tag `<Teleport to="body">` dalam hal penanganan layout bertumpuk (*z-index*).

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan Fungsi refresh()
*   **Fungsi `refresh()`**: Memaksa browser mengeksekusi ulang pemanggilan API `useApi('/volunteers')` guna menarik array data ter-update dari database server backend.
*   **Hubungan Sinkronisasi**: HTTP DELETE adalah operasi asinkron yang memodifikasi data di database server. Namun, layar visual browser client tidak mengetahui bahwa database di server sudah berkurang 1 baris. Jika kita lupa memanggil `refresh()`, baris tabel nama relawan yang baru saja kita hapus akan **tetap terlihat terpampang di layar browser**, membuat petugas admin bingung mengira tombol hapus rusak. Memanggil `refresh()` menyelaraskan wujud visual tabel dengan status riil database server.

### Jawaban 3: Resiko Z-Index Tanpa Teleport
*   Jika modal ditaruh di dalam baris tabel, CSS modal akan terkunci di bawah batasan CSS tabel.
*   Jika kontainer tabel memiliki properti `overflow: auto` (untuk scroll horizontal di HP), modal dialog besar Anda akan **terpotong ujungnya** dan tidak bisa melebar keluar tabel. 
*   Selain itu, jika baris tabel di bawahnya memiliki urutan render yang lebih baru, visual modal konfirmasi Anda rawan **tertimpa di lapisan belakang baris data lain**, merusak kemewahan desain antarmuka kita. `<Teleport>` melarikan fisik modal ke gerbang terluar `<body>` agar bebas melayang di atas segalanya.
