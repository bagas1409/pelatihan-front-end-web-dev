# Hari 63: Penanganan Token Kadaluarsa (JWT Expired)
## Mengatur Logout Otomatis & Pembersihan Cookies Sesi Petugas Saat Masa Berlaku Token Habis

Selamat datang di Hari 63! Hari ini kita akan mempelajari cara menangani **Token Kadaluarsa** (JWT Token Expiration) di sisi frontend.

Demi menjaga keamanan database medis PMI, setiap token JWT login dinas petugas UDD memiliki masa kedaluwarsa otomatis (misalnya token kadaluarsa setelah 2 jam). Jika token sudah kadaluarsa dan petugas masih mencoba mengklik tombol tabel atau mengklik menu simpan stok, database server Express.js akan menolak request tersebut dan melempar pesan error HTTP **`401 Unauthorized`**. Kita harus mengajarkan aplikasi Nuxt kita untuk menangkap error tersebut, lalu secara otomatis me-logout paksa petugas dan membersihkan Cookies dari memori laptop.

---

## 1. Kapan Token Kadaluarsa Harus Ditangani?

Kita menangani token kadaluarsa di dua tempat utama:
1.  **Secara Pasif (Saat Memuat Halaman - Client-Side Check)**: Sebelum halaman dimuat, kita membaca isi payload token JWT, menerjemahkannya, dan mengecek timestamp waktu kadaluarsa (*exp*).
2.  **Secara Aktif (Saat Mengirim Request API - Server-Side Response)**: Kapanpun backend merespon HTTP `401 Unauthorized`, itu adalah alarm utama untuk segera me-logout user secara otomatis.

---

## 2. Studi Kasus PMI: Logika Deteksi Token Expired di Pinia Store

Buka berkas Pinia Store Anda di **`stores/auth.js`** dan mari kita sisipkan fungsi pengecek waktu kadaluarsa token JWT secara lokal.

```javascript
// stores/auth.js (Versi Lengkap dengan Cek Kadaluarsa)
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export const useAuthStore = defineStore('auth', () => {
  const token = ref(null);
  const user = ref(null);

  // Getters mendeteksi apakah token sudah kadaluarsa
  const isTokenExpired = computed(() => {
    if (!token.value) return true;

    try {
      // Decode JWT Payload (Bagian tengah token yang dipisah tanda titik ".")
      const payloadBase64 = token.value.split('.')[1];
      const payloadDecoded = JSON.parse(atob(payloadBase64));
      
      const waktuSekarangDetik = Math.floor(Date.now() / 1000);
      
      // Bandingkan exp timestamp dari JWT dengan waktu laptop saat ini
      return payloadDecoded.exp < waktuSekarangDetik;

    } catch (err) {
      // Jika token korup/rusak formatnya, anggap kadaluarsa
      return true;
    }
  });

  const logoutPetugas = () => {
    token.value = null;
    user.value = null;
    navigateTo('/login');
  };

  // Fungsi pengecekan yang dipanggil di Middleware Rute
  const cekSesiValiditas = () => {
    if (isTokenExpired.value && token.value) {
      console.warn("Masa berlaku token telah habis! Mengeluarkan petugas otomatis.");
      logoutPetugas();
      return false;
    }
    return true;
  };

  return {
    token,
    user,
    isTokenExpired,
    logoutPetugas,
    cekSesiValiditas
  };
}, {
  persist: true
});
```

---

## 3. Menghubungkan Cek Sesi ke Middleware Rute (`middleware/auth.js`)

Buka berkas middleware rute pengaman Anda di `middleware/auth.js` dan tambahkan fungsi pemicu cek sesi:

```javascript
// middleware/auth.js
import { useAuthStore } from '~/stores/auth';

export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore();

  // 1. Cek apakah token ada secara fisik
  if (!authStore.token) {
    return navigateTo('/login');
  }

  // 2. Cek apakah token tersebut sudah kadaluarsa di memori cookie
  const sesiValid = authStore.cekSesiValiditas();
  if (!sesiValid) {
    // Jika tidak valid, otomatis diarahkan ke login oleh fungsi di dalam store
    return navigateTo('/login');
  }
});
```

---

## 4. Latihan Soal Mandiri
1. Amati rumus pembacaan payload token JWT: `token.value.split('.')[1]`.
2. Jelaskan struktur pembagian 3 bagian string pada token JWT (JSON Web Token) dan mengapa bagian index ke-1 (tengah) di-decode menggunakan fungsi JavaScript bawaan **`atob()`**.
3. Sebutkan apa akibat buruknya jika kita hanya mengandalkan pengecekan waktu lokal laptop (`Date.now()`) untuk memvalidasi kadaluarsa token, sedangkan petugas dinas sengaja merubah jam di sistem Windows mereka mundur 5 jam ke belakang.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Anatomi JWT Token & Fungsi atob()
*   **Struktur JWT**: JWT memiliki 3 bagian yang dipisahkan oleh tanda titik (`.`): **`Header.Payload.Signature`**.
    *   Index 0 (Header): Berisi algoritma enkripsi.
    *   Index 1 (Payload): Berisi data klaim user, role, dan batas waktu kadaluarsa (`exp`).
    *   Index 2 (Signature): Berisi kunci rahasia pembuktian keabsahan token.
*   **Fungsi `atob()`**: Digunakan untuk menerjemahkan (*Decode*) kembali string format **Base64** menjadi string teks JSON polos agar properti `.exp` di dalamnya bisa dibaca oleh JavaScript.

### Jawaban 3: Kelemahan Cek Waktu Client & Solusi Server Security
*   **Bahaya manipulasi jam**: Fungsi `Date.now()` membaca jam fisik laptop petugas. Jika petugas memundurkan jam sistem operasi Windows mereka, browser akan terkecoh mengira token tersebut belum kadaluarsa, padahal server backend aslinya sudah menyatakan token hangus.
*   **Solusinya**: Pengecekan waktu lokal di frontend hanyalah alat UX bantu mempermudah redirect. Penentu keamanan utama tetap berada di database backend: server backend Express.js Anda **wajib selalu menolak** request transaksi jika token yang dibawa terbukti kadaluarsa di jam server backend asli, dan mengembalikan status HTTP `401 Unauthorized` yang akan ditangkap oleh *HTTP Interceptor* frontend (Hari 65) untuk memaksa logout.
