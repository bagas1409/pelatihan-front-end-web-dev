# Hari 70: Fitur Setujui Permintaan CITO (HTTP PATCH)
## Mengubah Status Permintaan Darah Menjadi 'Disetujui' Menggunakan HTTP PATCH Secara Asinkron

Selamat datang di Hari 70! Hari ini kita akan menyelesaikan fitur penyetujuan transaksi darurat CITO menggunakan metode HTTP **`PATCH`** di Nuxt 3.

Saat ada panggilan CITO darurat dari rumah sakit mitra, petugas admin UDD PMI bertugas memverifikasi kesiapan kantong darah di kulkas logistik, lalu menekan tombol "Setujui Permintaan". Kita akan menembak endpoint API backend (`PATCH /api/permintaan/:id`) untuk merubah status transaksi tersebut dari *"Pending"* menjadi *"Disetujui / Terdistribusi"* tanpa perlu memuat ulang seluruh layar halaman.

---

## 1. Kegunaan HTTP PATCH vs PUT (Ulasan Cepat)

*   **`PUT` (Update Penuh)**: Mengharuskan kita mengirimkan seluruh payload objek data (jika ada yang terlewat, kolom tersebut rawan terhapus di database).
*   **`PATCH` (Update Parsial)**: Hanya mengirimkan 1 properti saja yang ingin diubah (dalam kasus ini: hanya mengirimkan `{ status: 'Disetujui' }`). Server backend memproses perubahan kolom status tersebut saja tanpa menyentuh data penting rumah sakit peminta maupun jumlah kantong darah asli.

---

## 2. Studi Kasus PMI: Halaman Kelola Permintaan CITO Darurat

Mari kita bangun halaman daftar permintaan CITO di `pages/cito/index.vue` lengkap dengan tombol aksi eksekusi status PATCH.

```html
<!-- pages/cito/index.vue -->
<template>
  <div class="space-y-6">
    <div>
      <h2 class="text-xl font-bold text-slate-800">🩸 Kelola Permintaan CITO</h2>
      <p class="text-xs text-slate-500 mt-1">Daftar permintaan darah darurat dari Rumah Sakit Mitra UDD</p>
    </div>

    <!-- Area List Permintaan CITO -->
    <div class="bg-white rounded-3xl border border-slate-200 shadow-sm overflow-hidden">
      <table class="w-full text-left border-collapse">
        <thead class="bg-slate-50 border-b text-xs font-bold text-slate-500 uppercase">
          <tr>
            <th class="py-4 px-6">Rumah Sakit</th>
            <th class="py-4 px-6">Golongan</th>
            <th class="py-4 px-6 text-center">Jumlah</th>
            <th class="py-4 px-6">Status Logistik</th>
            <th class="py-4 px-6 text-right">Tindakan</th>
          </tr>
        </thead>

        <tbody class="text-sm divide-y divide-slate-100">
          <tr v-for="cito in listCito" :key="cito.id" class="hover:bg-slate-50/50">
            <td class="py-4 px-6 font-bold text-slate-800">{{ cito.rs }}</td>
            <td class="py-4 px-6 font-mono font-bold text-red-500">{{ cito.gol }}</td>
            <td class="py-4 px-6 text-center font-bold">{{ cito.jumlah }} Bag</td>
            
            <!-- Badge Status -->
            <td class="py-4 px-6">
              <span 
                class="px-3 py-1 rounded-full text-[9px] font-bold uppercase tracking-wider border"
                :class="cito.status === 'Disetujui' 
                  ? 'bg-emerald-50 text-emerald-600 border-emerald-100' 
                  : 'bg-amber-50 text-amber-600 border-amber-100 animate-pulse'"
              >
                {{ cito.status }}
              </span>
            </td>

            <!-- Tombol Aksi PATCH -->
            <td class="py-4 px-6 text-right">
              <button 
                v-if="cito.status === 'Pending'"
                @click="setujuiCito(cito.id)"
                :disabled="loadingId === cito.id"
                class="bg-emerald-500 hover:bg-emerald-600 text-white font-extrabold text-[10px] px-3.5 py-2 rounded-xl transition-all disabled:opacity-40"
              >
                {{ loadingId === cito.id ? '⏳ Proses...' : 'Setujui' }}
              </button>
              <span v-else class="text-xs text-slate-400 font-bold">✓ Selesai</span>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue';

definePageMeta({
  layout: 'default',
  middleware: 'auth'
});

const { $toast } = useNuxtApp();
const loadingId = ref(null);

// Simulasi data permintaan CITO dari database server
const listCito = ref([
  { id: 101, rs: 'RSU Abdul Moeloek', gol: 'O', jumlah: 5, status: 'Pending' },
  { id: 102, rs: 'RS Urip Sumoharjo', gol: 'AB', jumlah: 2, status: 'Disetujui' },
  { id: 103, rs: 'RS Advent Bandar Lampung', gol: 'A', jumlah: 3, status: 'Pending' }
]);

// FUNGSI EKSEKUSI PATCH API
const setujuiCito = async (idCito) => {
  loadingId.value = idCito; // Nyalakan loading khusus untuk baris tombol yang diklik
  
  const API_PATCH_URL = `https://pmi-backend-api.onrender.com/api/donations/${idCito}`;

  try {
    // Tembak API PATCH secara asinkron
    await $fetch(API_PATCH_URL, {
      method: 'PUT', // Jika backend Anda menggunakan PUT untuk update sebagian, sesuaikan
      body: {
        status: 'Disetujui' // Kirim payload status baru saja
      }
    });

    // Cari objek cito di array lokal, ganti statusnya secara reaktif
    const item = listCito.value.find(c => c.id === idCito);
    if (item) {
      item.status = 'Disetujui';
    }

    $toast.success("✓ Permintaan CITO berhasil disetujui! Kurir disiagakan.");

  } catch (err) {
    console.error("Gagal mem-patch status:", err);
    $toast.error(`🚨 Gagal memproses permintaan: ${err.data?.message || err.message}`);
  } finally {
    loadingId.value = null; // Matikan loading
  }
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati letak penanda pembeda loading button: `:disabled="loadingId === cito.id"`.
2. Jelaskan fungsi dari penggunaan variabel state **`loadingId`** yang merekam ID baris tersebut dibandingkan jika kita hanya menggunakan variabel boolean sederhana seperti `const loading = ref(false)` di dalam tabel loop.
3. Sebutkan kelebihan pemrosesan status update lokal: `item.status = 'Disetujui'` (setelah API sukses) dari segi efisiensi penggunaan kuota internet petugas lapangan dibandingkan kita memanggil fungsi `refresh()` penuh ke server.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pengaturan Loading Baris Spesifik (Row-Level Loading)
*   **Masalah dengan Boolean Tunggal**: Jika kita memakai `const loading = ref(false)` (bernilai boolean polos) dan petugas mengklik tombol "Setujui" pada baris RS nomor 101, **seluruh tombol "Setujui" di baris 103 juga akan ikut berubah visual menampilkan spinner loading**. Ini membingungkan petugas karena seolah-olah seluruh baris diproses bersamaan.
*   **Keunggulan `loadingId`**: Dengan menyimpan nilai ID angka (`101`) di variabel `loadingId`, evaluasi `:disabled="loadingId === cito.id"` hanya bernilai `true` khusus untuk baris yang bersangkutan, membiarkan tombol di baris lain tetap bersih tak terpengaruh.

### Jawaban 3: Keunggulan Reaktivitas Lokal (Optimistic UI Update)
*   **Hemat Kuota & Lebih Cepat**: Memanggil `refresh()` penuh memaksa browser mengunduh ulang data puluhan baris logistik baru dari database server. 
*   Dengan langsung menyunting isi array lokal di memori RAM browser client setelah API sukses (`item.status = 'Disetujui'`), visual layar langsung ter-update seketika dalam waktu 0 milidetik tanpa memicu koneksi download data tambahan, menghemat penggunaan kuota internet mobile dinas petugas di lapangan.
