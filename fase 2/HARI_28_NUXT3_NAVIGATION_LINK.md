# Hari 28: Navigasi Antar Halaman di Nuxt 3
## Menggunakan NuxtLink & Router Programatis navigateTo() Tanpa Menyebabkan Kedipan Halaman (SPA Mode)

Selamat datang di Hari 28! Hari ini kita akan mempelajari cara berpindah halaman secara halus tanpa membuat browser mematung memuat ulang seluruh halaman dari awal menggunakan komponen **`<NuxtLink>`** dan fungsi navigasi programatis **`navigateTo()`**.

Jika di Hari 27 kita memicu perpindahan halaman menggunakan tag tautan HTML tradisional `<a href="...">`, Anda akan menyadari bahwa browser selalu berkedip memutar roda loading di tab atas. Itu dinamakan **Full Page Reload**, yang membuang data RAM dan memperlambat kenyamanan pengguna. Kita akan beralih ke navigasi bergaya **Single Page Application (SPA)** yang instan dan mulus.

---

## 1. Keunggulan Komponen `<NuxtLink>`

Komponen `<NuxtLink>` adalah pembungkus tag tautan cerdas bawaan Nuxt 3. Dia mendeteksi jika link tujuan berada di dalam server kita:
1.  **Navigasi SPA Tanpa Kedip**: Begitu diklik, dia tidak memuat ulang file index HTML. Dia hanya menukar komponen halaman di layar secara instan.
2.  **Prefetching Cerdas**: Saat tombol link terlihat di layar monitor monitor petugas, Nuxt secara diam-diam di latar belakang mengunduh file halaman tujuan tersebut. Ketika petugas mengkliknya, halaman langsung terbuka dalam milidetik tanpa jeda memuat (*Instant Loading*).

---

## 2. Navigasi Programatis dengan `navigateTo()`
Ada situasi di mana kita tidak bisa menggunakan tombol link biasa, melainkan harus menunggu suatu fungsi selesai dijalankan terlebih dahulu (seperti memproses validasi login sukses, baru memindahkan user ke dashboard). 

Di Vue 3 Composition API Nuxt, kita menggunakan fungsi helper **`navigateTo('/rute-tujuan')`**:

```javascript
const prosesLogin = async () => {
  const sukses = await API.login(credentials);
  if (sukses) {
    // Navigasi setelah logika Javascript selesai diproses
    await navigateTo('/dashboard');
  }
};
```

---

## 3. Studi Kasus PMI: Navbar Dashboard SPA & Redirect Tombol Login

Mari kita perbaiki kerangka menu navigasi dashboard agar berpindah rute secara SPA tanpa kedip.

```html
<template>
  <div class="flex min-h-screen bg-slate-50">
    
    <!-- SIDEBAR NAVIGASI -->
    <aside class="w-64 bg-slate-900 text-slate-300 p-6 space-y-6">
      <h2 class="font-bold text-white text-lg">UDD Lampung</h2>
      
      <nav class="flex flex-col gap-2">
        <!-- 
          Gunakan <NuxtLink> menggantikan <a href>
          Atribut href diganti menjadi "to"
          active-class -> Kelas CSS khusus yang aktif otomatis jika halaman sedang dibuka!
        -->
        <NuxtLink 
          to="/" 
          active-class="bg-slate-800 text-white font-bold" 
          class="px-4 py-3 rounded-xl hover:bg-slate-800 transition-colors"
        >
          📊 Dashboard
        </NuxtLink>

        <NuxtLink 
          to="/stok" 
          active-class="bg-slate-800 text-white font-bold"
          class="px-4 py-3 rounded-xl hover:bg-slate-800 transition-colors"
        >
          🩸 Kelola Stok
        </NuxtLink>

        <NuxtLink 
          to="/relawan" 
          active-class="bg-slate-800 text-white font-bold"
          class="px-4 py-3 rounded-xl hover:bg-slate-800 transition-colors"
        >
          👥 Relawan
        </NuxtLink>
      </nav>
    </aside>

    <!-- AREA UTAMA -->
    <main class="flex-1 p-8 space-y-6">
      <h1 class="text-xl font-bold text-slate-800">Uji Coba Formulir Login</h1>
      
      <div class="max-w-xs bg-white p-6 rounded-2xl border border-slate-200 shadow-sm space-y-4">
        <input type="password" v-model="token" placeholder="Masukkan Token Dinas" class="w-full px-3 py-2 border rounded-xl outline-none" />
        
        <!-- Pemicu fungsi navigasi programatis -->
        <button 
          @click="verifikasiDanMasuk" 
          class="w-full bg-red-500 hover:bg-red-600 text-white font-bold py-2 rounded-xl text-xs"
        >
          Masuk Server UDD
        </button>
      </div>
    </main>

  </div>
</template>

<script setup>
import { ref } from 'vue';

const token = ref('');

const verifikasiDanMasuk = async () => {
  if (token.value === '123') {
    // Pindahkan rute ke stok secara otomatis
    await navigateTo('/stok');
  } else {
    alert('Token Salah!');
  }
};
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati tag `<NuxtLink to="/stok" active-class="bg-slate-800">` di atas.
2. Jelaskan fungsi dari atribut **`active-class`** pada komponen `<NuxtLink>` tersebut dan bagaimana hal itu mempermudah kita memberi indikator visual menu mana yang sedang aktif dibuka oleh petugas admin.
3. Mengapa kita wajib menuliskan kata kunci **`await`** di depan fungsi navigasi programatis `navigateTo(...)` di dalam script setup?

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan Active Class
*   **Fungsi `active-class`**: Atribut ini bertugas mendeteksi kecocokan URL browser saat ini dengan nilai properti `to` pada tag `<NuxtLink>`.
*   **Kemudahan Visual**: Jika saat ini browser membuka alamat `/stok`, Nuxt secara otomatis menyuntikkan kelas CSS `bg-slate-800 text-white font-bold` ke dalam tag HTML link stok tersebut. Jika kita berpindah ke `/relawan`, kelas tersebut dicopot dari link stok dan otomatis berpindah menempel di link relawan. Developer tidak perlu menuliskan logika manual `if-else` untuk mengecek path URL saat ini.

### Jawaban 3: Await navigateTo
*   Fungsi `navigateTo` di Nuxt 3 mengembalikan objek **Promise**. Proses pembersihan memori halaman lama dan pemuatan skrip halaman baru berjalan secara asinkron.
*   Menuliskan `await` menjamin mesin JavaScript menyelesaikan pemindahan rute terlebih dahulu sebelum mengeksekusi instruksi baris di bawahnya (seperti mematikan loading state atau menghapus token temporer).
