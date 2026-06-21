# Hari 51: Nuxt 3 Plugins
## Mengintegrasikan Library Pihak Ketiga dengan Sistem Plugin Auto-Load

Selamat datang di Hari 51! Hari ini kita akan mempelajari cara menggunakan fitur **Nuxt Plugins** untuk mengintegrasikan library atau package JavaScript pihak ketiga (*3rd Party Libraries*) ke dalam ekosistem aplikasi frontend kita secara modular.

Saat kita ingin menambahkan komponen interaktif eksternal (seperti pemutar kalender tanggal yang mewah, charting grafik, atau sistem notifikasi toast), kita tidak boleh asal mengimpor file tersebut secara mentah-mentah di setiap halaman karena bisa merusak keselarasan hidrasi SSR. Nuxt menyediakan folder khusus bernama **`plugins/`** agar library luar diinisialisasi sekali sejak aplikasi pertama kali dijalankan.

---

## 1. Bagaimana Nuxt Plugins Bekerja?

Nuxt secara otomatis memindai direktori **`plugins/`** di root proyek Anda. 
*   Seluruh file di dalam folder tersebut akan dieksekusi secara otomatis saat aplikasi Nuxt pertama kali dimuat di server maupun client.
*   Di dalam file plugin, kita menggunakan fungsi helper **`defineNuxtPlugin`** dan kita bisa mengekspos (*provide*) fungsi khusus ke seluruh halaman web kita menggunakan simbol prefix dollar (**`$`**), contoh: `useNuxtApp().$toast`.

---

## 2. Struktur Dasar Penulisan Plugin

Berikut adalah pola standar menulis file plugin di Nuxt 3:

```javascript
// plugins/my-plugin.js
export default defineNuxtPlugin((nuxtApp) => {
  // nuxtApp menyediakan akses penuh ke seluruh instance aplikasi Vue dan Nuxt
  
  // Contoh mendaftarkan library / menyuntikkan fungsi kustom ke global
  return {
    provide: {
      sayHello: (nama) => `Halo ${nama}, selamat bertugas di UDD PMI!`
    }
  };
});
```

Cara memanggil fungsi di atas pada komponen Vue mana saja:
```html
<script setup>
// Ambil instance nuxtApp
const { $sayHello } = useNuxtApp();
console.log($sayHello('Budi')); // Output: Halo Budi, selamat bertugas di UDD PMI!
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati cara kerja fungsi penengah `defineNuxtPlugin` di atas.
2. Jika Anda membuat file baru bernama `plugins/analitik.client.js`, jelaskan makna dari penyisipan kata kustom **`.client`** di tengah nama file tersebut bagi proses Server-Side Rendering (SSR).
3. Sebutkan apa akibat buruknya jika kita mendaftarkan library chart yang berat (memanipulasi objek `document` fisik) di dalam plugin polos tanpa ekstensi `.client`.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Mengenal Plugin client-only (.client)
*   **Makna ekstensi `.client`**: Menginstruksikan Nuxt bahwa file plugin tersebut **hanya boleh dieksekusi di sisi browser client saja**, dan dilarang keras dijalankan saat server Node.js sedang merakit HTML awal.
*   **Alasan**: Banyak library pihak ketiga (seperti Google Analytics atau peta Leaflet) membutuhkan akses ke objek browser global seperti `window`, `document`, atau `navigator`. Karena objek-objek browser tersebut tidak ada di server Node.js, menjalankannya di server akan menyebabkan website langsung mogok error crash *"window is not defined"*.

### Jawaban 3: Bahaya Crash Server SSR
*   Jika library yang memanipulasi DOM browser diletakkan pada plugin polos (tanpa `.client`), Nuxt akan mencoba mengeksekusi plugin tersebut di server Node.js saat merakit halaman web pertama kali.
*   **Dampaknya**: Server Node.js akan langsung mogok (Error 500 / Crash total) sebelum HTML sempat dikirim ke browser, merusak kegunaan server rendering kita secara keseluruhan. Selalu gunakan penamaan `.client.js` atau `.server.js` untuk library yang terikat erat pada hardware masing-masing lingkungan kerja.
