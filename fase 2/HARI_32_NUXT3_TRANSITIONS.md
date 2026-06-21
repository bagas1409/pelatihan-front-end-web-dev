# Hari 32: Transisi Halaman & Animasi Elemen
## Menghidupkan Transisi Mulus Antar Rute Halaman Menggunakan pageTransition CSS

Selamat datang di Hari 32! Hari ini kita akan mempelajari cara meningkatkan kemewahan visual antarmuka kita menggunakan fitur **Page Transitions** (Transisi Halaman) bawaan Nuxt 3.

Sebuah portal aplikasi web yang premium tidak boleh berpindah halaman secara kaku (langsung berganti visual secara kasar dalam 1 milidetik). Kita akan menambahkan efek transisi memudar halus (*Fade In/Out*) dan menggeser lembut (*Slide*) saat petugas UDD mengklik menu sidebar berpindah dari halaman stok ke halaman database relawan.

---

## 1. Konsep Transisi Halaman di Nuxt 3

Nuxt 3 menggunakan sistem transisi bawaan Vue `<Transition>`. Saat terjadi perpindahan rute halaman dinamis:
1.  Nuxt secara otomatis menempelkan kelas CSS transisi pada halaman yang sedang **keluar** (seperti kelas `.page-leave-active`).
2.  Nuxt menempelkan kelas transisi pada halaman baru yang sedang **masuk** (seperti kelas `.page-enter-from`).
3.  Kita cukup menuliskan aturan animasi CSS untuk kelas-kelas tersebut di file CSS utama proyek kita.

---

## 2. Langkah Konfigurasi Transisi Memudar (Fade)

### Langkah 1: Nyalakan Fitur Transisi di `nuxt.config.ts`
Buka berkas konfigurasi `nuxt.config.ts` dan tambahkan properti `app.pageTransition` berikut:

```typescript
export default defineNuxtConfig({
  compatibilityDate: '2024-04-03',
  devtools: { enabled: true },
  modules: ['@nuxtjs/tailwindcss'],
  css: ['~/assets/css/main.css'],

  // Konfigurasi transisi global
  app: {
    pageTransition: { 
      name: 'halaman', // Nama awalan selector CSS kustom kita
      mode: 'out-in'    // Jalankan animasi keluar dulu, baru masukkan halaman baru
    }
  }
})
```

### Langkah 2: Tulis Aturan Animasi CSS di `assets/css/main.css`
Buka file CSS utama Anda, lalu tambahkan transisi menggunakan nama prefix `halaman` sesuai konfigurasi di atas:

```css
/* assets/css/main.css */

/* 1. Aturan durasi waktu transisi */
.halaman-enter-active,
.halaman-leave-active {
  transition: all 0.3s ease; /* Transisi berdurasi 300ms */
}

/* 2. Kondisi awal saat halaman masuk, & kondisi akhir saat halaman keluar */
.halaman-enter-from,
.halaman-leave-to {
  opacity: 0;                /* Memudar total */
  transform: translateY(8px); /* Sedikit bergeser naik/turun */
}
```

Setelah disimpan, buka browser dan klik tautan menu navigasi sidebar Anda. Anda akan melihat halaman memudar dan meluncur halus ke atas saat berganti rute!

---

## 3. Latihan Soal Mandiri
1. Amati pengaturan `mode: 'out-in'` pada berkas konfigurasi di atas.
2. Jelaskan konsekuensi tampilan visual perpindahan halaman jika kita merubah nilai properti mode tersebut menjadi `'in-out'` atau menghapusnya sama sekali.
3. Tuliskan baris CSS kustom tambahan jika Anda ingin membuat efek transisi halaman bergeser horizontal dari arah kanan ke kiri (Slide Right-to-Left) menggantikan efek memudar vertikal di atas.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya Mode Out-In
*   **Mode `out-in` (Keluar lalu Masuk - Terbaik)**: Halaman lama akan dianimasikan memudar hingga hilang total terlebih dahulu. Setelah hilang dari layar DOM, barulah halaman baru masuk memudar membesar. Transisi terasa sangat rapi dan berurutan.
*   **Mode `in-out` atau Tanpa Mode (Masuk dan Keluar Bersamaan)**: Halaman baru akan digambar di layar **sebelum halaman lama sempat hilang**. Akibatnya, kedua halaman tersebut akan bertumpuk berantakan secara vertikal (satu terdorong ke bawah layar) selama 0.3 detik durasi animasi berjalan, membuat layout web tampak rusak berantakan sesaat sebelum stabil kembali.

### Jawaban 3: Aturan CSS Slide Horizontal Transition:
```css
/* Transisi Slide Horizontal */
.halaman-enter-active,
.halaman-leave-active {
  transition: transform 0.25s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.25s ease;
}

/* Kondisi awal masuk dari kanan (+30px), dan kondisi keluar meluncur ke kiri (-30px) */
.halaman-enter-from {
  opacity: 0;
  transform: translateX(30px);
}
.halaman-leave-to {
  opacity: 0;
  transform: translateX(-30px);
}
```
Aturan ini memberikan efek modern seperti aplikasi seluler native di mana halaman meluncur dinamis ke arah samping kiri.
