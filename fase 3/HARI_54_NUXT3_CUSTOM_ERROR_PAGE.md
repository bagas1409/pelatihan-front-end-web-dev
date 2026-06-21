# Hari 54: Penanganan Error Halaman Kustom (error.vue)
## Membuat Halaman Peringatan 404 & 500 yang Cantik Menggunakan clearError()

Selamat datang di Hari 54! Hari ini kita akan mempelajari cara menangani kesalahan navigasi dan padamnya server API menggunakan halaman khusus: **Custom Error Page** via berkas **`error.vue`** di Nuxt 3.

Jika pengguna mengetik rute URL yang ngawur/salah (seperti `/stok-darah-salah`), Nuxt secara default akan menampilkan halaman hitam putih bawaan berisi pesan error *"Page not found: 404"*. Halaman bawaan tersebut terlihat sangat tidak profesional. Kita akan merakit halaman error kustom yang cantik dan menyediakan tombol penyelamat untuk mengembalikan pengguna kembali ke beranda dengan selamat.

---

## 1. Aturan Penulisan File `error.vue`

Berbeda dengan file halaman biasa yang ditaruh di folder `pages/`, berkas **`error.vue`** adalah berkas khusus yang **wajib diletakkan langsung di root direktori proyek Anda** (satu tingkat dengan `app.vue`).

*   Nuxt 3 akan otomatis mencegat seluruh error (seperti kode HTTP 404 Not Found, 500 Server Crash, dll) dan merender file `error.vue` ini menggantikan visual halaman yang rusak.

---

## 2. Studi Kasus PMI: Membuat Berkas `error.vue` yang Premium

1.  Buat file baru bernama **`error.vue`** langsung di root folder proyek Anda.
2.  Isi dengan struktur desain minimalis Tailwind CSS berikut:

```html
<!-- error.vue -->
<template>
  <div class="min-h-screen bg-slate-50 flex items-center justify-center p-6 text-center">
    <div class="max-w-md w-full bg-white p-8 rounded-3xl border border-slate-200 shadow-sm space-y-6">
      
      <!-- Icon Peringatan -->
      <div class="mx-auto w-16 h-16 bg-red-50 text-red-500 rounded-full flex items-center justify-center text-3xl">
        🚨
      </div>

      <!-- Kode & Pesan Error -->
      <div class="space-y-2">
        <h1 class="text-4xl font-black text-slate-800">{{ error.statusCode }}</h1>
        <h2 class="text-base font-bold text-slate-700">
          {{ error.statusCode === 404 ? 'Halaman Tidak Ditemukan' : 'Terjadi Kesalahan Server' }}
        </h2>
        <p class="text-xs text-slate-400 leading-relaxed px-4">
          {{ error.message || 'Maaf, sistem UDD PMI mengalami kegagalan memuat rute data yang Anda minta.' }}
        </p>
      </div>

      <!-- Tombol Penyelamat Kembali -->
      <!-- 
        PENTING: Jangan gunakan <NuxtLink> di halaman error. 
        Gunakan fungsi clearError() untuk membersihkan state error dari memori browser!
      -->
      <button 
        @click="kembaliKeBeranda" 
        class="w-full bg-slate-800 hover:bg-slate-900 text-white font-bold py-3 rounded-xl text-xs transition-colors active:scale-95"
      >
        Kembali ke Beranda UDD
      </button>

    </div>
  </div>
</template>

<script setup>
// Menangkap properti error yang disuntikkan secara otomatis oleh Nuxt
defineProps({
  error: Object
});

const kembaliKeBeranda = () => {
  // Fungsi clearError bertugas membersihkan status error di memori RAM browser
  // lalu memindahkan rute kembali ke beranda utama '/' secara aman
  clearError({ redirect: '/' });
};
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati tombol kembali di atas yang menggunakan fungsi `@click="kembaliKeBeranda"`.
2. Mengapa kita sangat dilarang keras menggunakan tag navigasi biasa seperti `<NuxtLink to="/">` untuk memindahkan pengguna keluar dari halaman error? Jelaskan apa dampaknya bagi status reaktivitas Vue.
3. Sebutkan bagaimana cara memicu halaman error kustom ini secara sengaja dari dalam fungsi script setup halaman biasa (misalnya ketika data relawan hasil fetch API bernilai kosong/null).

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya clearError()
*   **Bahaya Tanpa `clearError()`**: Jika kita menggunakan `<NuxtLink to="/">`, browser secara fisik memang akan berpindah ke rute beranda. Namun, karena state error internal Nuxt **belum dibersihkan dari memori RAM**, Nuxt akan mendeteksi aplikasi masih berada di bawah bayang-bayang status error. 
*   **Dampaknya**: Komponen visual halaman beranda baru tidak akan terender, atau visual layar akan tetap menampilkan kotak error yang sama meskipun URL browser sudah berubah menjadi `/`. Fungsi `clearError({ redirect: '/' })` memastikan memori dibersihkan total sebelum perpindahan rute dijalankan.

### Jawaban 3: Pemicuan Error Manual (showError)
Kita bisa memicu layar error kustom ini secara programatis dari berkas Javascript menggunakan fungsi helper **`showError`** bawaan Nuxt:

```javascript
const { data: relawan } = await useApi('/volunteers/701');

if (!relawan.value) {
  // Paksa Nuxt membuang tampilan halaman saat ini dan menggantinya dengan halaman Error 404
  throw createError({ 
    statusCode: 404, 
    statusMessage: 'Relawan tidak ditemukan',
    fatal: true // Memastikan server me-render halaman error.vue secara fatal
  });
}
```
