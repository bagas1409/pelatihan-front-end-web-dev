# Hari 65: Interceptor HTTP Global
## Menangkap Respons HTTP 401 Unauthorized Secara Global untuk Mencegah Kebocoran Sesi Kadaluarsa

Selamat datang di Hari 65! Hari ini kita akan mempelajari teknik pertahanan keamanan API terpenting di frontend: **HTTP Interceptor** (Pencegat HTTP) di Nuxt 3.

Di Hari 63 kita sudah membuat pengecek kadaluarsa pasif. Namun, jika token JWT kadaluarsa tepat saat petugas sedang aktif mengisi formulir, dan mereka menekan tombol simpan, server backend akan menolak dengan status HTTP **`401 Unauthorized`**. Kita butuh sistem pengawas global (**Interceptor**) yang bertugas mendengarkan respon error 401 tersebut di balik layar untuk secara instan memotong jalur program, menampilkan toast peringatan merah, membersihkan kuki, dan menendang paksa user kembali ke halaman login.

---

## 1. Konsep Interceptor pada Fetch Nuxt 3

Helper `$fetch` bawaan Nuxt 3 menyediakan fungsi callback siklus request-response (*Hooks*):
*   **`onRequest`**: Mencegat request sebelum meluncur ke internet (bisa untuk menyuntikkan header token otomatis).
*   **`onResponseError`**: Mencegat response yang gagal dari server sebelum response tersebut sampai ke baris kode `try-catch` halaman kita.

---

## 2. Studi Kasus PMI: Menyematkan Interceptor pada useApi

Buka kembali berkas custom composable Anda di **`composables/useApi.js`** dan mari kita modifikasi strukturnya agar memiliki pencegat error 401 yang tangguh.

```javascript
// composables/useApi.js (Versi Lengkap dengan HTTP Interceptor)
import { useAuthStore } from '~/stores/auth';

export const useApi = (path, options = {}) => {
  const authStore = useAuthStore();
  const token = authStore.token;

  const BASE_URL = 'https://pmi-backend-api.onrender.com/api';

  const defaultHeaders = {
    Accept: 'application/json',
    ...(token ? { Authorization: `Bearer ${token}` } : {})
  };

  return useFetch(`${BASE_URL}${path}`, {
    ...options,
    headers: {
      ...defaultHeaders,
      ...options.headers
    },

    // =======================================================
    // INTERCEPTOR: MENANGKAP ERROR RESPONS DARI SERVER API
    // =======================================================
    onResponseError({ request, response, options }) {
      console.warn(`[API Interceptor Detect] Status Error: ${response.status}`);

      // Tangkap error 401 Unauthorized (Token kadaluarsa / Palsu)
      if (response.status === 401) {
        console.error("Token JWT dinas tidak sah atau kadaluarsa!");
        
        // 1. Bersihkan token dan data user di Pinia Store & Cookies
        authStore.logoutPetugas();

        // 2. Tampilkan Toast error melayang (jika dijalankan di client browser)
        if (process.client) {
          const { $toast } = useNuxtApp();
          $toast.error("🚨 Sesi dinas Anda telah berakhir. Silakan login kembali!");
        }

        // 3. Alihkan rute secara paksa ke halaman login
        navigateTo('/login');
      }
    }
  });
};
```

---

## 3. Latihan Soal Mandiri
1. Amati blok pemeriksaan: `if (response.status === 401)` di atas.
2. Jelaskan fungsi dari pemeriksaan kondisional `if (process.client)` sebelum memicu pemanggilan notifikasi `$toast` di dalam interceptor di atas.
3. Sebutkan kelebihan penanganan error HTTP 401 terpusat di file interceptor dibandingkan kita menuliskan blok penangkap `catch (err) { if (err.status === 401) { ... } }` di setiap fungsi submit form pada 30 file halaman web yang berbeda.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya Pengecekan Lingkungan Client Only
*   **Fungsi `process.client`**: Merupakan variabel penanda lingkungan bawaan Nuxt 3. Dia bernilai `true` jika kode sedang dieksekusi di dalam browser client, dan bernilai `false` jika kode dieksekusi di server Node.js (SSR).
*   **Mencegah Crash SSR**: Library notifikasi toast (`vue3-toastify`) terikat erat dengan visual layar browser. Jika terjadi error 401 saat server Node.js sedang mencoba menembak API di belakang layar, memanggil `$toast` di server akan memicu crash total karena server tidak memiliki tampilan visual. Pengecekan `process.client` memastikan toast hanya dipicu setelah halaman mendarat di browser client.

### Jawaban 3: Keunggulan Sentralisasi HTTP Interceptor
*   **Menghindari Duplikasi Kode (DRY)**: Kita tidak perlu mengotori puluhan file halaman dengan baris kode logout paksa yang berulang-ulang di setiap catch error.
*   **Satu Gerbang Kendali Keamanan**: Kapanpun backend memperbaharui sistem otorisasi (misalnya: merubah respon token mati menjadi HTTP `403 Forbidden` untuk role tertentu), kita cukup mengganti parameter pengecekan di satu file `useApi.js` ini saja untuk melindungi seluruh halaman secara serentak.
```
[FASE 4: SISTEM KEAMANAN INTERCEPTOR HTTP GLOBAL SUKSES]
```
