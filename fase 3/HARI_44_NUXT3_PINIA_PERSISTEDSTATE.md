# Hari 44: Persist State Pinia
## Menyimpan Otomatis Token Login Sesi Petawan PMI Ke LocalStorage / Cookies Agar Tidak Hilang Saat F5 Refresh

Selamat datang di Hari 44! Hari ini kita akan menyelesaikan permasalahan kritis pada state management: **State Persistence** (Pengawetan State).

Saat kita menyimpan token login di Pinia di Hari 43, cobalah untuk menekan tombol **F5 (Refresh)** di peramban browser Anda. Anda akan mendeteksi bahwa sistem otomatis keluar (*Logout*) secara mendadak. 
*   *Mengapa?* Karena secara default, semua state Pinia disimpan di dalam memori RAM komputer. Saat halaman direfresh, browser membuang seluruh memori RAM lama dan merakit ulang data dari nol (menyebabkan token bernilai `null` kembali). Kita akan mempelajari cara mengunci data store di memori fisik peramban secara otomatis.

---

## 1. Strategi Penyimpanan Fisik: LocalStorage vs Cookie

Untuk mengunci data agar tidak terbuang saat refresh, kita memiliki dua pilihan media penyimpanan:
*   **LocalStorage**: Menyimpan data secara permanen di harddisk komputer browser client.
    *   *Kelemahan*: Hanya bisa dibaca oleh JavaScript di sisi client. Jika server Nuxt (SSR) mencoba memeriksa status login sebelum mengirim halaman, server akan gagal membaca localStorage dan memicu masalah *Hydration Mismatch*.
*   **Cookies (Kuki)**: Menyimpan data yang dikirim bolak-balik secara otomatis di setiap header request HTTP antara browser client dan server.
    *   *Keunggulan*: Sangat aman, mendukung Server-Side Rendering (SSR) karena server Nuxt bisa mendeteksi status login sejak milidetik pertama sebelum HTML dirakit.

---

## 2. Menggunakan Pustaka `@pinia-plugin-persistedstate`

Untuk mengotomatiskan proses penyimpanan, kita akan menggunakan pustaka resmi yang didukung penuh oleh Nuxt 3: **`pinia-plugin-persistedstate`**. Pustaka ini secara cerdas mendeteksi perubahan state di Pinia, lalu menyalin nilainya ke Cookie secara real-time di balik layar tanpa coding manual.

### Langkah 1: Instal Dependensi
Jalankan perintah instalasi di terminal root proyek Anda:
```bash
npm install @pinia-plugin-persistedstate/nuxt
```

### Langkah 2: Daftarkan di `nuxt.config.ts`
Buka file konfigurasi Nuxt dan daftarkan modul persisten tersebut:

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  compatibilityDate: '2024-04-03',
  devtools: { enabled: true },
  modules: [
    '@nuxtjs/tailwindcss',
    '@pinia/nuxt',
    '@pinia-plugin-persistedstate/nuxt' // Daftarkan modul persisten di sini!
  ]
})
```

---

## 3. Studi Kasus PMI: Mengunci Token Login di `stores/auth.js`

Setelah modul terpasang, kita hanya perlu menambahkan opsi konfigurasi **`persist: true`** di parameter ketiga fungsi `defineStore` kita.

Buka kembali berkas **`stores/auth.js`** dan setel konfigurasinya seperti ini:

```javascript
// stores/auth.js (Versi Persisten)
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export const useAuthStore = defineStore('auth', () => {
    
    // ... isi state, getters, dan actions tetap sama persis seperti Hari 43 ...
    const token = ref(null);
    const user = ref(null);

    return {
        token,
        user,
        // ... return properti lainnya ...
    };
}, {
    // MENYALAKAN FITUR AUTO-PERSIST KE COOKIES
    persist: {
        // Menggunakan storage cookie agar kompatibel dengan SSR Nuxt
        storage: persistedState.cookiesWithOptions({
            sameSite: 'lax',
            secure: process.env.NODE_ENV === 'production',
            maxAge: 60 * 60 * 24 * 7 // Sesi dikunci aktif selama 7 hari penuh
        })
    }
});
```

Simpan file, jalankan server dev, login ke website, lalu tekan tombol refresh (F5) berulang-kali. Anda akan mendeteksi sesi login Anda tetap terkunci aktif dan tidak ter-logout secara mendadak lagi!

---

## 4. Latihan Soal Mandiri
1. Amati opsi `storage: persistedState.cookiesWithOptions(...)` di atas.
2. Jelaskan bahaya keamanan bagi token JWT petugas UDD jika kita menyimpannya menggunakan media `localStorage` standar di komputer warnet atau laptop publik yang bebas digunakan oleh siapa saja.
3. Sebutkan apa fungsi dari opsi konfigurasi `secure: true` pada penyetelan cookie di atas dan mengapa dia dinonaktifkan (`false`) selama tahap server lokal (development).

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kerentanan Keamanan LocalStorage
*   **Bahaya Pencurian Token (XSS)**: Data di `localStorage` bersifat bebas dibaca oleh skrip JavaScript apa saja yang berjalan di halaman web tersebut. Jika website Anda terinfeksi serangan injeksi kode berbahaya oleh hacker (*Cross-Site Scripting / XSS*), hacker bisa dengan sangat mudah mencuri token dinas login petugas Anda hanya dengan 1 baris kode: `const tokenMilikPmi = localStorage.getItem('auth')`. Hacker kemudian bisa masuk menyamar sebagai admin dan memalsukan logistik darah.
*   **Resiko Laptop Publik**: Berbeda dengan cookie yang memiliki masa tenggang kadaluarsa otomatis, data di `localStorage` akan menetap di harddisk selamanya sampai ada yang membersihkan riwayat browser secara manual, rawan disalahgunakan oleh pengguna laptop berikutnya.

### Jawaban 3: Opsi Secure Cookies
*   **Fungsi `secure: true`**: Menginstruksikan browser bahwa file cookie tersebut **hanya boleh dikirim jika koneksi menggunakan protokol keamanan terenkripsi HTTPS**. Cookie dilarang dikirim jika menggunakan HTTP polos tanpa SSL.
*   **Alasan Dinonaktifkan di Dev**: Karena saat mengoding di laptop lokal kita menggunakan alamat `http://localhost:3000` (HTTP biasa tanpa SSL). Jika kita memaksakan `secure: true` di komputer lokal, browser akan menolak menyimpan cookie tersebut, menyebabkan fitur login persisten kita tampak seolah-olah rusak/error saat dicoding.
