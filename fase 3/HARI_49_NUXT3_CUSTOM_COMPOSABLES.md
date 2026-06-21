# Hari 49: Custom Composables di Nuxt 3
## Memecah Logika Bisnis yang Kompleks Agar Dapat Digunakan Kembali di Berbagai Halaman

Selamat datang di Hari 49! Hari ini kita akan mempelajari konsep **Custom Composables** (Fungsi Komposisi Kustom) di Nuxt 3.

Saat menulis kode JavaScript di `<script setup>`, kita seringkali mengulang-ulang logika asinkron yang sama di berbagai halaman (seperti memproses format uang rupiah, memanggil status loading ganda, atau mengecek izin login). Menulis baris kode yang sama berulang kali melanggar prinsip **DRY (Don't Repeat Yourself)**. Custom Composables memungkinkan kita mengekstrak logika-logika reaktif tersebut ke dalam satu berkas fungsi independen di folder **`composables/`** yang secara otomatis bisa dipanggil di mana saja.

---

## 1. Apa itu Composable?

### 💡 Analogi Dunia Nyata: "Gantungan Kotak Perkakas Sabuk Pinggang Mekanik"
Bayangkan Anda adalah montir keliling UDD PMI Lampung. Di setiap mobil unit donor keliling, Anda butuh obeng bintang, tang pemotong selang selang medis, dan plester steril.
*   **Tanpa Composable**: Di setiap unit mobil, Anda membeli dan menaruh obeng, tang, dan plester baru di jok belakang secara berantakan. Anda membuang uang dan waktu merapikannya.
*   **Dengan Composable (Sabuk Perkakas)**: Anda merakit satu sabuk pinggang khusus berisi obeng, tang, dan plester yang pas di pinggang Anda. Ke manapun Anda pergi berpindah mobil dinas, Anda cukup memakai sabuk pinggang tersebut. Seluruh alat reaktif langsung berada di genggaman tangan Anda siap digunakan.

---

## 2. Aturan Folder `composables/` di Nuxt 3

Nuxt 3 secara otomatis memindai seluruh file yang terletak di folder **`composables/`** root proyek Anda:
1.  Setiap berkas disarankan mengekspor fungsi dengan nama awalan **`use`** (contoh: `useKonversiDarah`, `useFormatTgl`).
2.  Nuxt mendeteksi ekspor tersebut dan mengimpornya secara otomatis (*Auto-Imports*). Kita tidak perlu mengetikkan baris `import` di halaman halaman utama kita.

---

## 3. Studi Kasus PMI: Membuat Composable Formatter & Status Stok

Mari kita rancang composable untuk memformat angka persediaan darah dan memberikan indikator bahaya stok kritis.

### Langkah 1: Buat Berkas `composables/usePersediaan.js`
Buat folder `composables` di root proyek Anda, lalu ciptakan file `usePersediaan.js`:

```javascript
// composables/usePersediaan.js
import { ref } from 'vue';

export const usePersediaan = () => {
  
  // 1. Fungsi biasa untuk memformat teks kantong
  const formatKantong = (jumlah) => {
    if (jumlah === undefined || jumlah === null) return '0 Bag';
    return `${jumlah} Bag Darah`;
  };

  // 2. Fungsi menentukan warna label bahaya logistik
  const dapatkanWarnaStatus = (jumlah) => {
    if (jumlah < 15) {
      return {
        bg: 'bg-red-50 text-red-600 border-red-100',
        label: '🚨 KRITIS (BUTUH CITO)'
      };
    }
    return {
      bg: 'bg-emerald-50 text-emerald-600 border-emerald-100',
      label: '✓ AMAN'
    };
  };

  // Kembalikan fungsi agar bisa di-import komponen halaman luar
  return {
    formatKantong,
    dapatkanWarnaStatus
  };
};
```

---

### Langkah 2: Menggunakan Composable di Halaman `pages/stok.vue`

Kita tinggal memanggil instansi `usePersediaan()` di dalam halaman kita tanpa baris import!

```html
<!-- pages/stok-demo.vue -->
<template>
  <div class="max-w-md mx-auto p-6 bg-white rounded-3xl border mt-8 space-y-4">
    <h3 class="font-bold text-slate-800">Laporan Logistik UDD</h3>
    
    <div class="space-y-3">
      <div v-for="item in stok" :key="item.gol" class="flex justify-between items-center py-2 border-b">
        <div>
          <span class="font-bold">Golongan {{ item.gol }}</span>
          <!-- Menggunakan fungsi formatKantong dari composable -->
          <p class="text-xs text-slate-400 mt-0.5">{{ formatKantong(item.jumlah) }}</p>
        </div>
        
        <!-- Menggunakan fungsi warna dari composable -->
        <span 
          class="px-3 py-1 rounded-full text-[10px] font-bold border"
          :class="dapatkanWarnaStatus(item.jumlah).bg"
        >
          {{ dapatkanWarnaStatus(item.jumlah).label }}
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// Memanggil custom composable secara instan (Auto-Imported)
const { formatKantong, dapatkanWarnaStatus } = usePersediaan();

const stok = ref([
  { gol: 'O', jumlah: 8 },  // Kritis
  { gol: 'A', jumlah: 45 }  // Aman
]);
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati bagaimana fungsi-fungsi di dalam file `usePersediaan.js` dikembalikan.
2. Jelaskan perbedaan mendasar antara file **Custom Composable** dengan file helper **Utility JavaScript biasa** (seperti file `utils/math.js` polos) dalam hal kemampuan mengakses State Reaktif Vue (seperti `ref`, `computed`, dan lifecycle `onMounted`).
3. Sebutkan nama awalan wajib yang direkomendasikan saat menamai nama berkas dan nama fungsi ekspor di folder `composables/`.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Perbedaan Composable vs Utility JS Biasa
*   **Utility JS Biasa**: Murni hanya berisi fungsi matematika atau pemrosesan string murni (seperti `const kaliDua = (x) => x * 2`). Fungsi ini **tidak bisa menggunakan state reaktif Vue** di dalamnya.
*   **Custom Composable**: Merupakan fungsi yang dijalankan di dalam siklus setup Vue. Dia memiliki kemampuan istimewa untuk membuat state reaktif internal sendiri (`const loading = ref(false)`), memantau data (`watch`), menaruh fungsi kalkulasi (`computed`), hingga mendengarkan siklus hidup komponen (`onMounted`). Composable bertindak seperti potongan program *mini-component* tanpa template visual.

### Jawaban 3: Aturan Penamaan Composable
*   **Aturan Awalan `use`**: Nama file dan nama fungsi ekspor wajib diawali dengan huruf kecil kata **`use`** diikuti huruf besar kata berikutnya (*camelCase*), contoh: `useAuth`, `useApi`, `useCart`.
*   **Alasan**: Membantu developer membedakan secara instan di area template mana fungsi yang bertindak sebagai hooks reaktif (memiliki state internal) dan mana fungsi utilitas javascript murni.
