# Hari 64: Nuxt 3 Shared State - useState
## Berbagi State Ringan Antar Komponen Tanpa Perlu Membuat File Pinia Store Baru

Selamat datang di Hari 64! Hari ini kita akan mempelajari fungsi pembantu manajemen state bawaan Nuxt 3: **`useState()`**.

Meskipun kita sudah menguasai Pinia di Hari 42 untuk state global yang besar, membuat file store baru di folder `stores/` hanya untuk berbagi 1 variabel reaktif kecil (seperti: status menu sidebar terbuka/tertutup, atau data filter pencarian sementara) terasa sangat berlebihan (*Overkill*) dan memboroskan jumlah berkas file proyek. Nuxt 3 memecahkan masalah ini dengan menyediakan composable `useState` sebagai media penampung state global instan tanpa konfigurasi.

---

## 1. Apa itu `useState` di Nuxt 3?

`useState` adalah alternatif dari `ref` Vue 3 yang memiliki kemampuan **Shared State** (Berbagi Nilai) lintas komponen dan ramah Server-Side Rendering (SSR).

*   **`const x = ref(0)`**: Nilai variabel hanya hidup di dalam file komponen tersebut saja. Jika dipanggil di server (SSR), datanya rawan bocor ke request user lain (*State Leakage*).
*   **`const x = useState('kunci-unik', () => 0)`**: Nilai variabel terikat aman pada koneksi user yang bersangkutan, dan **bisa diakses serta diubah langsung** oleh komponen anak, halaman, maupun layout lain secara instan selama menggunakan string kunci unik (`kunci-unik`) yang sama.

---

## 2. Studi Kasus PMI: Sinkronisasi Tombol Hamburg Menu Sidebar

Mari kita rancang fitur toggle buka-tutup sidebar. Tombol pemicunya berada di Header atas (komponen A), namun bagian menu sidebarnya berada di Sidebar kiri (komponen B). Kita akan sinkronkan keduanya menggunakan `useState`.

### Langkah 1: Buat Komponen Header (`components/layout/HeaderUDD.vue`)
```html
<!-- components/layout/HeaderUDD.vue -->
<template>
  <header class="h-16 bg-white border-b px-6 flex items-center justify-between">
    <!-- Tombol Hamburg Toggle -->
    <button 
      @click="toggleSidebar" 
      class="p-2 hover:bg-slate-100 rounded-xl text-slate-600 transition-colors"
    >
      ☰
    </button>
    <span class="text-xs font-bold text-slate-500">PMI Provinsi Lampung</span>
  </header>
</template>

<script setup>
// Gunakan useState dengan kunci unik 'sidebar-buka'
// Parameter kedua adalah callback nilai awal (default: true)
const isSidebarOpen = useState('sidebar-buka', () => true);

const toggleSidebar = () => {
  isSidebarOpen.value = !isSidebarOpen.value; // Balikkan nilainya
};
</script>
```

---

### Langkah 2: Buat Komponen Sidebar (`components/layout/SidebarUDD.vue`)
Komponen sidebar membaca status reaktif dari kunci yang sama untuk merubah lebar CSS-nya.

```html
<!-- components/layout/SidebarUDD.vue -->
<template>
  <!-- Lebar lebar sidebar berubah dinamis dari w-64 menjadi w-0 (tersembunyi) -->
  <aside 
    class="bg-slate-900 text-slate-300 transition-all duration-300 overflow-hidden flex flex-col justify-between"
    :class="isSidebarOpen ? 'w-64 p-6' : 'w-0 p-0'"
  >
    <div class="w-52">
      <h2 class="font-bold text-white text-lg mb-6">Portal UDD</h2>
      <nav class="space-y-2 text-sm">
        <NuxtLink to="/" class="block px-3 py-2 rounded-xl hover:bg-slate-800">📊 Dashboard</NuxtLink>
        <NuxtLink to="/stok" class="block px-3 py-2 rounded-xl hover:bg-slate-800">🩸 Stok Darah</NuxtLink>
      </nav>
    </div>
  </aside>
</template>

<script setup>
// Membaca state reaktif yang sama dari memori global Nuxt
const isSidebarOpen = useState('sidebar-buka');
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati inisialisasi state: `useState('sidebar-buka', () => true)`.
2. Mengapa pada komponen kedua (`SidebarUDD.vue`) kita tidak perlu menuliskan kembali fungsi callback nilai awal (`() => true`) di parameter kedua `useState`? Jelaskan arsitekturnya.
3. Sebutkan kelebihan `useState` dibandingkan menggunakan Pinia Store dalam hal kemudahan integrasi coding cepat (*Rapid Development*).

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Caching State useState
*   **Arsitektur Caching**: Nuxt menggunakan parameter kunci `'sidebar-buka'` sebagai jangkar. Saat komponen pertama (`HeaderUDD.vue`) dipasang, Nuxt membuat dan menyimpan state bernilai `true` tersebut di memori RAM internal.
*   Saat komponen kedua (`SidebarUDD.vue`) memanggil `useState('sidebar-buka')`, Nuxt mendeteksi kunci tersebut **sudah terdaftar aktif** di memori. Nuxt langsung memberikan referensi alamat memori reaktif yang sama tanpa memproses fungsi callback nilai awal kembali, menjamin kedua komponen terikat pada 1 variabel yang identik.

### Jawaban 3: Keunggulan useState vs Pinia
*   **Tanpa Membuat File Baru**: Kita tidak perlu mengotori direktori proyek dengan puluhan file kecil di folder `stores/` (seperti `stores/sidebarStore.js`, `stores/themeStore.js`).
*   **Praktis & Instan**: Cukup panggil `useState('nama-kunci')` di script setup manapun, dan data reaktif langsung terbagi aman di seluruh level komponen, memotong durasi setup arsitektur state management secara signifikan untuk variabel-variabel kecil.
