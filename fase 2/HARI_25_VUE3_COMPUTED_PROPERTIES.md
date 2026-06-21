# Hari 25: Computed Properties di Vue 3
## Menggunakan computed() untuk Melakukan Kalkulasi Nilai Secara Efisien & Cerdas di Memori RAM

Selamat datang di Hari 25! Hari ini kita akan mempelajari fitur performa terpenting di Vue 3: **Computed Properties** (Properti Terhitung) menggunakan fungsi bawaan **`computed()`**.

Seringkali di halaman web admin DonorKu, kita harus menampilkan data hasil kalkulasi dari variabel state (seperti menghitung total kantong darah yang kritis, atau menjumlahkan seluruh stok). Jika kita menulis rumus matematika tersebut langsung di dalam area template HTML, browser akan dipaksa menghitung ulang rumus tersebut di setiap siklus render kecil, yang memboroskan tenaga CPU. `computed()` lahir untuk menyimpan hasil hitungan secara cerdas di cache memori.

---

## 1. Apa itu Computed Property?

### 💡 Analogi Dunia Nyata: "Buku Catatan Total Belanjaan & Cache Ingatan Kasir UDD"
Bayangkan Anda adalah petugas kasir apotek darah PMI.
*   **Tanpa Computed (Rumus Kasar di Kertas)**: Setiap kali ada pengunjung melirik ke kasir bertanya: *"Berapa total stok darah kita hari ini?"*, Anda terpaksa mengambil pulpen, membuka buku besar, menjumlahkan 10 baris angka dari atas ke bawah: `12 + 45 + 9 + 98 = 164`. Jika 10 orang bertanya dalam 1 menit, Anda menghitung ulang 10 kali di kertas. Tangan Anda lelah dan waktu terbuang.
*   **Dengan Computed (Cache Ingatan Cerdas Kasir)**: Saat pertama kali data stok diisi, Anda menghitung sekali: `164`. Anda mengingat angka tersebut di kepala Anda. Ketika 10 orang bertanya, Anda langsung menjawab *"164"* seketika tanpa menyentuh pulpen sama sekali. 
    *   Ingatan Anda (cache) hanya akan di-update jika Anda mendengar kabar ada 1 kantong darah keluar (ada state yang berubah). Selama data dasar tidak berubah, Anda menggunakan ingatan cache lama Anda.

---

## 2. Struktur Sintaks computed()

Untuk menggunakan `computed()`, kita mengimpornya dari `vue` dan membungkus rumus hitungan kita di dalam fungsi return callback-nya.

```javascript
import { ref, computed } from 'vue';

const stokA = ref(10);
const stokB = ref(15);

// computed otomatis memantau stokA dan stokB
const totalStok = computed(() => {
  console.log("Kalkulator computed berjalan menghitung..."); // Hanya tercetak sekali!
  return stokA.value + stokB.value;
});
```

---

## 3. Studi Kasus PMI: Menghitung Indikator Kritis Persediaan UDD

Mari kita buat halaman dashboard yang memuat daftar stok darah, dan menampilkan jumlah kategori stok yang berstatus "Kritis" secara reaktif menggunakan `computed()`.

```html
<template>
  <div class="max-w-md mx-auto p-6 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-4">
    <h3 class="font-bold text-slate-800 border-b pb-3">Ringkasan UDD</h3>

    <!-- List Stok Darah -->
    <ul class="space-y-2">
      <li v-for="item in stokDarah" :key="item.gol" class="flex justify-between items-center text-sm">
        <span>Golongan {{ item.gol }}</span>
        <div class="flex items-center gap-4">
          <input 
            type="number" 
            v-model.number="item.jumlah" 
            class="w-16 px-2 py-1 border rounded text-right"
          />
          <span :class="item.jumlah < 10 ? 'text-red-500 font-bold' : 'text-slate-500'">
            {{ item.jumlah }} Bag
          </span>
        </div>
      </li>
    </ul>

    <!-- RINGKASAN DATA MENGGUNAKAN COMPUTED -->
    <div class="pt-4 border-t border-slate-100 space-y-2 text-xs">
      <div class="flex justify-between font-bold text-slate-700">
        <span>Total Persediaan:</span>
        <!-- Memanggil totalStok tanpa kurung tutup () karena bertindak seperti properti data biasa -->
        <span>{{ totalStok }} Kantong</span>
      </div>
      <div class="flex justify-between font-bold text-red-500" v-if="jumlahKritis > 0">
        <span>Golongan Darah Kritis (Di bawah 10 Bag):</span>
        <span>{{ jumlahKritis }} Golongan</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const stokDarah = ref([
  { gol: "A", jumlah: 15 },
  { gol: "B", jumlah: 8 }, // Kritis
  { gol: "AB", jumlah: 3 }, // Kritis
  { gol: "O", jumlah: 98 }
]);

// 1. COMPUTED: Menghitung total stok
const totalStok = computed(() => {
  return stokDarah.value.reduce((total, item) => total + item.jumlah, 0);
});

// 2. COMPUTED: Menyaring jumlah golongan yang kritis
const jumlahKritis = computed(() => {
  const listKritis = stokDarah.value.filter(item => item.jumlah < 10);
  return listKritis.length;
});
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati pemanggilan `{{ totalStok }}` di template atas.
2. Mengapa kita tidak diperbolehkan menuliskan kurung buka-tutup di belakang nama computed (seperti `{{ totalStok() }}`) saat memanggilnya di HTML? Jelaskan perbedaannya dengan pemanggilan fungsi *Methods* biasa.
3. Sebutkan kelebihan computed property dibandingkan dengan fungsi JavaScript biasa dalam hal performa rendering halaman web.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pemanggilan Tanpa Kurung ()
*   Computed Property dirancang oleh Vue 3 untuk bertindak sebagai **Properti Data Statis**, bukan fungsi pengeksekusi perintah. Vue membungkus fungsi getter tersebut di balik layar sehingga secara visual dan akses luar dia berperilaku persis seperti variabel state biasa (seperti variabel `nama` atau `usia`).
*   Jika kita memanggil `totalStok()`, Vue akan melemparkan error karena `totalStok` bukanlah instansi fungsi *Callable*, melainkan objek Properti Reaktif.

### Jawaban 3: Keunggulan Performa Cache Computed
*   **Computed (Memiliki Cache)**: Computed property mengetahui variabel dependensi apa saja yang ia pantau (misal: array `stokDarah`). Selama angka jumlah stok tidak ada yang diubah oleh petugas, berulang kalipun browser menggambar ulang visual layar karena interaksi tombol lain, computed **tidak akan pernah menjalankan ulang rumus perulangan reduksinya**. Dia langsung mengambil angka `124` dari ingatan RAM cache instan.
*   **Fungsi Biasa (Methods - Tanpa Cache)**: Fungsi biasa tidak memiliki ingatan cache. Setiap kali browser menggambar ulang layar, fungsi tersebut akan dipaksa berjalan menyisir isi array dari nol berulang-ulang, yang memboroskan tenaga CPU jika isi data array mencapai ribuan baris.
