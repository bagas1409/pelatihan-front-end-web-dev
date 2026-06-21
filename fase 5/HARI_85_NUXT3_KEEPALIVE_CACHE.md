# Hari 85: Optimasi Cache Komponen dengan KeepAlive
## Menjaga State Isian Form & Posisi Scroll Tetap Utuh Saat Pindah Tab Menggunakan KeepAlive

Selamat datang di Hari 85! Hari ini kita akan mempelajari teknik optimasi performa dan pengalaman pengguna (**UX**) tingkat lanjut: **Component Caching** menggunakan tag bawaan Vue 3 **`<KeepAlive>`** di Nuxt 3.

Saat petugas admin PMI sedang sibuk mengetik formulir pendaftaran relawan baru, lalu ia berpindah menu sebentar ke tab stok darah untuk memeriksa persediaan, dan kemudian mengklik kembali tombol menu form relawan, secara default **seluruh ketikan form yang melelahkan tersebut akan terhapus bersih kembali kosong**. Ini dikarenakan Vue menghancurkan (*destroy*) instance komponen saat tidak aktif. Kita akan belajar cara menahan komponen di memori RAM agar data tetap utuh saat ditinggalkan sementara.

---

## 1. Bagaimana KeepAlive Bekerja?

*   **Tanpa KeepAlive (Siklus Hancur)**: Komponen aktif $\rightarrow$ Dihancurkan (`onUnmounted`) $\rightarrow$ Dibuat Ulang dari Nol (`onMounted`). Seluruh variabel state `ref` kembali ke nilai awal.
*   **Dengan KeepAlive (Siklus Hibernasi)**: Komponen aktif $\rightarrow$ Dinonaktifkan (`onDeactivated`) $\rightarrow$ Disimpan di RAM latar belakang $\rightarrow$ Diaktifkan kembali (`onActivated`). State, posisi scroll layar, dan ketikan input tetap utuh 100% seperti semula.

---

## 2. Studi Kasus PMI: Menerapkan KeepAlive pada Halaman Tab Panel

Untuk menyalakan fitur ini pada transisi halaman utama Nuxt, kita bisa mengaktifkan properti **`keepalive: true`** di dalam `definePageMeta` pada masing-masing file halaman yang ingin kita kunci memorinya.

Mari kita buat halaman form input tervalidasi yang tidak akan pernah terhapus ketikannya meskipun kita berpindah halaman:

### Berkas Halaman: `pages/relawan/baru-keepalive.vue`

```html
<!-- pages/relawan/baru-keepalive.vue -->
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border mt-8 space-y-6">
    <div>
      <h3 class="font-bold text-slate-800 text-base">✍️ Form Relawan (Cache Terjaga)</h3>
      <p class="text-xs text-slate-400 mt-1">
        Coba ketik kolom di bawah, lalu klik menu lain, kemudian kembali lagi ke sini. Ketikan Anda dijamin tidak hilang!
      </p>
    </div>

    <div class="space-y-4 text-xs font-bold text-slate-500">
      <div>
        <label class="block uppercase mb-2">Nama Lengkap</label>
        <input 
          type="text" 
          v-model="namaInput" 
          class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 font-medium" 
          placeholder="Ketik nama di sini..."
        />
      </div>

      <!-- Menampilkan status siklus hidup komponen di layar -->
      <div class="p-4 bg-slate-50 rounded-xl font-mono text-[10px] text-slate-400">
        STATUS SIKLUS: {{ statusSiklus }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, onActivated, onDeactivated } from 'vue';

definePageMeta({
  layout: 'default',
  middleware: 'auth',
  
  // ========================================================
  // KUNCI EMAS OPTIMASI: Nyalakan Caching KeepAlive Halaman!
  // ========================================================
  keepalive: true 
});

const namaInput = ref('');
const statusSiklus = ref('Setup Awal');

// 1. Hook siklus hidup standar
onMounted(() => {
  console.log("Komponen dipasang pertama kali.");
});

onUnmounted(() => {
  // Baris ini tidak akan pernah terpanggil saat pindah halaman karena memori disimpan
  console.log("Komponen dihancurkan!"); 
});

// 2. Hook siklus hidup khusus untuk KeepAlive Component
onActivated(() => {
  statusSiklus.value = '✓ Aktif Kembali (Data RAM Dimuat)';
  console.log("Petugas kembali membuka halaman ini. State di-restore.");
});

onDeactivated(() => {
  statusSiklus.value = '💤 Hibernasi (Disimpan di RAM Latar Belakang)';
  console.log("Petugas meninggalkan halaman. Menyimpan posisi memori...");
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati hook lifecycle khusus: `onActivated` dan `onDeactivated` di atas.
2. Jelaskan fungsi dari hook `onActivated` pada komponen yang dibungkus KeepAlive dan mengapa hook standar `onMounted` tidak akan dieksekusi kembali saat kita membuka kedua kalinya halaman tersebut.
3. Sebutkan kelemahan dari penyalahgunaan fitur `keepalive: true` jika dipasang di **seluruh** (100+) halaman web admin portal PMI tanpa filter tebang pilih dari aspek pemakaian kapasitas RAM komputer/HP petugas.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Siklus Hidup KeepAlive Component
*   **Fungsi `onActivated()`**: Merupakan hook khusus Vue yang menggantikan peran `onMounted` khusus untuk komponen yang dikunci di memori. Dia dipicu setiap kali komponen dibangunkan kembali dari mode hibernasi latar belakang.
*   **Mengapa Mounted Dilewati**: Karena instance komponen secara fisik **tidak pernah dihancurkan dari memori RAM**. Struktur HTML-nya hanya dilepaskan sementara dari visual browser. Saat dibuka kembali, Vue langsung mengambil struktur memori yang sudah ada tanpa merakit ulang dari nol, sehingga event lifecycle `onMounted` dilewati total.

### Jawaban 3: Resiko Jebolnya RAM Akibat Over-caching
*   **Memory Bloat (RAM Jebol)**: Menyimpan komponen di memori RAM latar belakang membutuhkan kapasitas memori fisik komputer/HP yang nyata. 
*   Jika 100 halaman dipasang `keepalive: true`, browser petugas lapangan terpaksa menahan ratusan objek variabel, cache gambar, dan canvas grafik yang tebal secara bersamaan. Akibatnya, pemakaian RAM browser akan meroket naik, menyebabkan HP dinas petugas terasa panas melambat (*Lagging*), dan browser rawan meledak crash kehabisan tenaga (*Out of Memory crash*).
*   **Solusi Bijak**: Gunakan `keepalive` hanya pada halaman form input panjang atau tabel filter pencarian yang membutuhkan penahanan posisi scroll. Hindari memasangnya pada halaman info statis ringan.
