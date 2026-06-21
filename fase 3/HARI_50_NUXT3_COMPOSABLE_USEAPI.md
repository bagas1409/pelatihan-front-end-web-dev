# Hari 50: Membuat Composable useApi
## Membangun Custom Fetch Wrapper Sebagai Pusat Kendali Base URL API & Token JWT Dinas

Selamat datang di Hari 50! Hari ini kita akan merancang sebuah custom composable yang paling penting bagi keamanan data dan integrasi API: **`useApi`**.

Saat memanggil server API backend Express.js di Hari 40 dan 48, kita mengalami dua keterbatasan besar:
1.  Kita terpaksa mengetikkan alamat base URL domain server API backend yang panjang secara berulang-ulang di setiap file.
2.  Setiap kali kita memanggil endpoint API yang terkunci (seperti menghapus data stok darah atau menambah relawan), kita harus menulis header otorisasi JWT Token manual berulang kali: `headers: { Authorization: 'Bearer ...' }`.
Kita akan memecahkan masalah ini dengan membuat pembungkus fetch kustom (`useApi`) yang secara otomatis menyuntikkan token dinas login petugas dari Pinia ke setiap request API secara transparan.

---

## 1. Konsep Custom Fetch Wrapper di Nuxt 3

Nuxt 3 menyediakan helper **`$fetch.create()`** atau **`useFetch`** kustom. Kita bisa membuat fungsi composable baru yang bertugas:
*   Membaca alamat server dari konfigurasi environment.
*   Mengambil token JWT aktif yang tersimpan aman di Pinia Auth Store (Hari 43).
*   Menyuntikkan token tersebut ke dalam objek header request HTTP sebelum sinyal data dikirim ke internet.

---

## 2. Studi Kasus PMI: Membuat Composable `composables/useApi.js`

1.  Buat berkas baru bernama **`useApi.js`** di dalam folder `composables/` proyek Nuxt Anda.
2.  Isi dengan baris kode pembungkus kustom berikut:

```javascript
// composables/useApi.js
import { useAuthStore } from '~/stores/auth';

export const useApi = (path, options = {}) => {
  // 1. Ambil token dinas aktif dari Pinia Auth Store
  const authStore = useAuthStore();
  const token = authStore.token;

  // 2. Tentukan domain utama server backend Express.js
  const BASE_URL = 'https://pmi-backend-api.onrender.com/api';

  // 3. Susun konfigurasi header default
  const defaultHeaders = {
    Accept: 'application/json',
    // Jika token terdeteksi ada di cookies/pinia, suntikkan secara otomatis!
    ...(token ? { Authorization: `Bearer ${token}` } : {})
  };

  // 4. Jalankan useFetch Nuxt 3 dengan opsi yang sudah digabung secara cerdas
  return useFetch(`${BASE_URL}${path}`, {
    ...options,
    headers: {
      ...defaultHeaders,
      ...options.headers // Biarkan header kustom lain menimpa jika ada
    }
  });
};
```

---

## 3. Cara Menggunakan useApi di Halaman Halaman `pages/`

Dengan adanya `useApi`, penulisan kode fetch kita di halaman utama menjadi sangat singkat, bersih, dan aman dari bahaya kelupaan token JWT:

### Skenario Lama (Tanpa useApi - Panjang & Rawan Bocor):
```javascript
const auth = useAuthStore();
const { data } = await useFetch('https://pmi-backend-api.onrender.com/api/volunteers', {
  headers: {
    Authorization: `Bearer ${auth.token}`
  }
});
```

### Skenario Baru (Dengan useApi - Sangat Bersih):
```javascript
// Cukup menuliskan endpoint ujungnya saja. Token JWT diurus otomatis di balik layar!
const { data, pending, error } = await useApi('/volunteers');
```

---

## 4. Latihan Soal Mandiri
1. Amati kode penggabungan objek pada header: `...(token ? { Authorization: ... } : {})`.
2. Jelaskan fungsi dari penggunaan operator sebaran objek tiga titik (**Spread Operator `...`**) pada baris kode di atas dan apa yang terjadi jika kita tidak menyertakannya saat variabel `token` bernilai kosong (`null`).
3. Sebutkan kelebihan dari segi keamanan data jika kita menyentralisasi penyuntikkan header Authorization JWT di satu berkas `useApi.js` dibandingkan menyebarkannya di belasan file halaman web.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Spread Operator Dinamis (Header)
*   **Fungsi `...`**: Menggabungkan (*Merge*) properti di dalam objek bersyarat secara instan ke dalam objek header utama.
*   **Logika Kondisional**: Jika `token` bernilai `null` (misal di halaman login umum), bagian `token ? ...` akan menghasilkan objek kosong `{}`. Spread operator mengabaikan objek kosong tersebut sehingga header Authorization tidak pernah ditulis.
*   **Akibat jika tanpa `...`**: Jika kita menulis objek mentah tanpa sebaran: `headers: { token ? { Authorization: ... } : null }`, browser akan memunculkan error karena struktur objek di dalam objek menjadi tidak valid, merusak format request HTTP.

### Jawaban 3: Keunggulan Sentralisasi Keamanan API
*   **Menghindari Bug Kelupaan**: Developer tidak perlu takut lupa menyisipkan header token saat membuat halaman CRUD relawan baru. Selama halaman tersebut memakai `useApi()`, token JWT dipastikan terpasang aman.
*   **Kemudahan Rotasi API**: Jika di masa depan backend bermigrasi menggunakan Authorization bertipe kustom (seperti `ApiKey ...` menggantikan `Bearer ...`), kita hanya perlu menyunting 1 baris kode saja di file `useApi.js` untuk memperbarui sistem keamanan di seluruh halaman website secara serentak.
```
[FASE 3: KOMPOSISI CUSTOM FETCH WRAPPER USEAPI SUKSES]
```
