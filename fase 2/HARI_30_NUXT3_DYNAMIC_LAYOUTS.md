# Hari 30: Layout Dinamis di Nuxt 3
## Mengatur Layout Berbeda untuk Halaman Login (Auth) vs Halaman Dashboard Admin

Selamat datang di Hari 30! Hari ini kita akan mempelajari cara mengelola kerangka halaman web menggunakan folder **`layouts/`** di Nuxt 3.

Saat membangun aplikasi web administrasi DonorKu PMI, kita memiliki dua jenis tampilan utama yang bertolak belakang:
1.  **Layout Login (Auth)**: Halaman bersih tanpa menu sidebar, tanpa navbar atas, hanya ada 1 kotak form di tengah layar monitor (`Auth Layout`).
2.  **Layout Panel Kerja (Dashboard)**: Memiliki menu navigasi sidebar di kiri dan baris profil di atas sepanjang waktu (`Default Layout`).
Kita akan belajar cara memisahkan kedua jenis kerangka ini dan menukarnya secara dinamis berdasarkan halaman yang dibuka.

---

## 1. Bagaimana Sistem Layout Nuxt 3 Bekerja?

Nuxt memindai folder **`layouts/`** di root proyek Anda:
*   **`layouts/default.vue`**: Adalah tata letak standar default yang akan otomatis membungkus semua halaman web Anda kecuali Anda meminta layout lain secara manual.
*   Di dalam setiap file layout, kita wajib menuliskan tag **`<slot />`**. Tag ini adalah lubang jendela tempat Nuxt memasukkan dan merender kode HTML dari file halaman `pages/` kita.

---

## 2. Studi Kasus PMI: Membuat Layout Default & Layout Auth

Mari kita buat dan daftarkan kedua jenis layout ini di proyek Nuxt 3 kita.

### Langkah 1: Buat Berkas Layout Default (`layouts/default.vue`)
Buat folder `layouts/` di root proyek Anda, lalu buat file `default.vue`:

```html
<!-- layouts/default.vue -->
<template>
  <div class="flex min-h-screen bg-slate-50">
    
    <!-- SIDEBAR KIRI -->
    <aside class="w-64 bg-slate-900 text-slate-300 p-6 flex flex-col justify-between">
      <div>
        <h2 class="font-bold text-white text-lg mb-6">Portal DonorKu</h2>
        <nav class="space-y-2">
          <NuxtLink to="/" class="block px-3 py-2 rounded-xl hover:bg-slate-800">📊 Dashboard</NuxtLink>
          <NuxtLink to="/stok" class="block px-3 py-2 rounded-xl hover:bg-slate-800">🩸 Kelola Stok</NuxtLink>
        </nav>
      </div>
      <div class="text-xs text-slate-500 border-t pt-4">Admin UDD v1.0</div>
    </aside>

    <!-- AREA KONTEN UTAMA -->
    <div class="flex-1 flex flex-col">
      <header class="h-16 bg-white border-b px-8 flex items-center justify-between">
        <span class="font-bold text-slate-700">PMI Provinsi Lampung</span>
        <NuxtLink to="/login" class="text-xs text-red-500 font-bold">Keluar</NuxtLink>
      </header>

      <!-- JENDELA TEMPAT MERENDER HALAMAN PAGES -->
      <main class="p-8 flex-1">
        <slot />
      </main>
    </div>

  </div>
</template>
```

---

### Langkah 2: Buat Berkas Layout Login/Auth (`layouts/auth.vue`)
Buat file baru di folder yang sama bernama `auth.vue`:

```html
<!-- layouts/auth.vue -->
<template>
  <!-- Layout minimalis terpusat di tengah layar -->
  <div class="min-h-screen bg-slate-100 flex items-center justify-center p-6">
    <div class="w-full max-w-sm bg-white p-8 rounded-3xl border border-slate-200 shadow-sm space-y-6">
      
      <!-- JENDELA TEMPAT MERENDER HALAMAN LOGIN -->
      <slot />

    </div>
  </div>
</template>
```

---

### Langkah 3: Menentukan Layout di Halaman Halaman `pages/`
Secara default, halaman `/login` akan ikut dibungkus sidebar default. Kita harus menyuruh halaman login menggunakan layout `auth.vue` menggunakan fungsi pembantu **`definePageMeta`** di dalam script setup.

### Berkas Halaman Login (`pages/login.vue`):
```html
<!-- pages/login.vue -->
<template>
  <div class="text-center space-y-4">
    <h2 class="text-xl font-bold text-slate-800">Login Petugas PMI</h2>
    <p class="text-xs text-slate-500">Masukkan kredensial dinas Anda</p>
    
    <input type="text" placeholder="Username" class="w-full px-3 py-2 border rounded-xl outline-none" />
    <button class="w-full bg-red-500 text-white font-bold py-2 rounded-xl text-sm">
      Masuk
    </button>
  </div>
</template>

<script setup>
// Menentukan layout kustom untuk halaman ini
definePageMeta({
  layout: 'auth' // Sesuai nama berkas "layouts/auth.vue"
});
</script>
```

---

## 3. Menghubungkan Layout di `app.vue`
Agar sistem layout bekerja secara dinamis di seluruh halaman, kita harus membungkus tag `<NuxtPage />` di dalam tag **`<NuxtLayout>`** di file `app.vue` kita.

```html
<!-- app.vue -->
<template>
  <div class="font-sans">
    <!-- NuxtLayout bertugas mendeteksi layout apa yang aktif di halaman saat ini -->
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>
```

---

## 4. Latihan Soal Mandiri
1. Amati pembungkusan di dalam file `app.vue` di atas.
2. Jelaskan fungsi dari tag **`<slot />`** pada berkas layout dan apa yang terjadi jika kita lupa menuliskan tag tersebut di dalam berkas `layouts/default.vue`.
3. Sebutkan cara menonaktifkan penggunaan layout secara total di suatu halaman (misalnya untuk halaman cetak laporan bersih tanpa dibungkus layout apapun).

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya Tag Slot
*   **Fungsi `<slot />`**: Bertindak sebagai wadah kosong penampung konten dinamis. Ketika pengguna membuka rute `/stok`, Nuxt memuat file `layouts/default.vue`, lalu menyuntikkan isi konten dari `pages/stok.vue` tepat di posisi tag `<slot />` berada.
*   **Akibat jika dihapus**: Halaman web hanya akan menampilkan menu sidebar dan navbar atas saja. Seluruh tombol tabel stok dan tulisan isi halaman kerja utama tidak akan pernah digambar di layar browser karena pintu masuknya tersumbat/dihapus.

### Jawaban 3: Menonaktifkan Layout
Untuk mematikan layout secara total pada halaman tertentu, kita menyuplai nilai boolean `false` ke opsi properti layout di dalam helper `definePageMeta`:

```javascript
definePageMeta({
  layout: false // Halaman akan dirender polosan murni tanpa pembungkus layout apapun
});
```
