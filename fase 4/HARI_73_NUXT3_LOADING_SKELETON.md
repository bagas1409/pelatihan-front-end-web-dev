# Hari 73: Desain Loading Skeleton Premium
## Mengganti Teks Loading Kuno Dengan Kerangka Animasi Denyut Abu-abu Tailwind CSS

Selamat datang di Hari 73! Hari ini kita akan mempelajari cara meningkatkan kualitas pengalaman pengguna (**UX - User Experience**) menggunakan **Loading Skeleton** (Kerangka Pemuat Konten).

Saat aplikasi web mengambil data daftar relawan atau riwayat donor dari server backend, layar monitor tidak boleh dibiarkan kosong polos atau hanya menampilkan teks tulisan *"Memuat..."* yang terkesan lamban. Kita akan mendesain kerangka balok abu-abu yang meniru bentuk baris tabel asli dan membuatnya berdenyut halus menggunakan bantuan kelas Tailwind CSS **`animate-pulse`**. Pengguna merasa website bekerja lebih cepat saat disuguhi kerangka skeleton dibandingkan teks loading polos.

---

## 1. Filosofi Desain Skeleton Loader

*   **Menyamarkan Latensi**: Skeleton loader memberikan ilusi bahwa konten halaman hampir selesai dimuat, mengalihkan perhatian mata pengguna dari durasi tunda koneksi internet.
*   **Bentuk Identik**: Layout kerangka balok abu-abu wajib menyerupai struktur visual asli dari komponen yang sedang dijemput datanya (misal: bentuk lingkaran untuk foto avatar profil, balok persegi panjang untuk teks nama).

---

## 2. Studi Kasus PMI: Komponen Skeleton Laporan Stok Darah

Mari kita rakit komponen skeleton khusus untuk baris logistik darah di berkas **`components/skeleton/StokSkeleton.vue`**.

```html
<!-- components/skeleton/StokSkeleton.vue -->
<template>
  <div class="space-y-4">
    <!-- Pemicu animasi denyut global (animate-pulse) -->
    <div 
      v-for="i in jumlahBaris" 
      :key="i" 
      class="bg-white p-6 rounded-3xl border border-slate-100 flex items-center justify-between animate-pulse"
    >
      <div class="flex items-center gap-4">
        <!-- 1. Skeleton Avatar Lingkaran (Meniru Ikon Golongan Darah) -->
        <div class="w-12 h-12 bg-slate-200 rounded-full"></div>
        
        <!-- 2. Skeleton Teks Judul & Sub-judul -->
        <div class="space-y-2">
          <div class="h-4 bg-slate-200 rounded w-24"></div>
          <div class="h-3 bg-slate-200 rounded w-32"></div>
        </div>
      </div>

      <!-- 3. Skeleton Tombol Aksi Kanan -->
      <div class="h-8 bg-slate-200 rounded-xl w-20"></div>
    </div>
  </div>
</template>

<script setup>
// Menerima properti jumlah baris skeleton yang ingin digambar
defineProps({
  jumlahBaris: {
    type: Number,
    default: 3
  }
});
</script>
```

---

## 3. Cara Menerapkan Skeleton di Halaman Utama

Kita tinggal menggunakan percabangan `v-if="pending"` untuk memilah kapan menggambar skeleton dan kapan menggambar data asli:

```html
<!-- pages/stok-demo.vue -->
<template>
  <div class="max-w-md mx-auto p-6 space-y-6">
    <h3 class="font-bold text-slate-800">🩸 Laporan Logistik Darah UDD</h3>

    <!-- TAMPILKAN SKELETON JIKA DATA SEDANG MEMUAT -->
    <SkeletonStokSkeleton v-if="pending" :jumlahBaris="3" />

    <!-- TAMPILKAN DATA ASLI JIKA SUDAH SELESAI FETCH -->
    <div v-else class="space-y-4">
      <div v-for="item in dataStok" :key="item.id" class="bg-white p-6 rounded-3xl border flex items-center justify-between">
        <div class="flex items-center gap-4">
          <div class="w-12 h-12 bg-red-100 text-red-500 rounded-full flex items-center justify-center font-bold">
            {{ item.gol }}
          </div>
          <div>
            <h4 class="font-bold text-sm">{{ item.rs }}</h4>
            <p class="text-xs text-slate-400">Tersedia: {{ item.jumlah }} Bag</p>
          </div>
        </div>
        <button class="bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-[10px] px-4 py-2 rounded-xl">
          Detail
        </button>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue';

const pending = ref(true);
const dataStok = ref([]);

// Simulasi jeda loading internet selama 2 detik
setTimeout(() => {
  dataStok.value = [
    { id: 1, gol: 'O', rs: 'Gudang Pusat UDD', jumlah: 98 },
    { id: 2, gol: 'A', rs: 'Cabang Metro', jumlah: 45 }
  ];
  pending.value = false; // Matikan loading
}, 2000);
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati penggunaan kelas Tailwind `bg-slate-200` pada elemen skeleton di atas.
2. Jelaskan mengapa warna balok skeleton dianjurkan menggunakan warna abu-abu tipis (seperti `slate-200` atau `gray-200`) daripada menggunakan warna kontras yang mencolok (seperti merah `red-300` atau kuning `yellow-300`).
3. Sebutkan kelebihan dari segi kenyamanan mata pengguna (*Visual Shift Prevention*) menggunakan skeleton loader dibandingkan teks loading polos yang seringkali membuat tombol-tombol melompat mendadak sesaat setelah data termuat.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pemilihan Warna Netral Skeleton
*   **Warna Abu-abu Netral**: Warna abu-abu redup menandakan elemen tersebut bersifat **pasif / belum aktif**. 
*   **Akibat jika kontras**: Menggunakan warna mencolok (seperti merah berkedip) akan membuat mata pengguna tegang dan mengira terjadi error alarm kritis pada aplikasi, merusak psikologi ketenangan pengguna (*Visual Stress*). Warna abu-abu halus memberikan kenyamanan visual yang ramah bagi mata petugas admin.

### Jawaban 3: Mencegah Pergeseran Layout (CLS - Cumulative Layout Shift)
*   **Masalah Pergeseran**: Teks loading polos `"Memuat..."` hanya memakan tinggi ruang 20 pixel. Saat data asli (berupa 3 baris card setinggi 300 pixel) mendadak ter-render, seluruh layout di bawahnya (seperti tombol footer) akan **terdorong melompat ke bawah secara mendadak**. Ini membuat petugas rawan salah klik tombol secara tidak sengaja.
*   **Solusi Skeleton**: Dengan mendesain skeleton yang memiliki tinggi dan lebar yang identik dengan card asli, kontainer halaman sudah mem-booking ruang kosong di layar sejak awal. Saat data asli masuk, dia langsung mengisi ruang skeleton tersebut dengan mulus tanpa memicu pergeseran lompatan layout (*Visual Layout Shift*).
