# Hari 43: Membuat Store Pinia Pertama (Auth Store)
## Mengelola Sesi Login Petugas & Token JWT Menggunakan defineStore Setup Syntax

Selamat datang di Hari 43! Hari ini kita akan mempraktikkan cara membuat berkas Store Pinia pertama kita bertajuk **`stores/auth.js`**.

Store ini akan menjadi pusat kendali keamanan website admin DonorKu. Dia bertugas menyimpan token akses JWT, merekam informasi biodata petugas yang sedang aktif login, serta menyediakan fungsi *Actions* asinkron untuk memproses pemanggilan API login ke server backend Express.js.

---

## 1. Menyusun Struktur Store Menggunakan Setup Syntax Vue 3

Sama seperti membuat komponen Vue 3, di Pinia kita menuliskan `defineStore` dengan gaya composition API:
*   Variabel `ref()` bertindak sebagai **State**.
*   Properti `computed()` bertindak sebagai **Getters**.
*   Fungsi biasa bertindak sebagai **Actions**.

---

## 2. Studi Kasus PMI: Membuat Berkas `stores/auth.js`

1.  Buat folder baru bernama **`stores`** di root direktori proyek Nuxt Anda.
2.  Buat file baru di dalamnya bernama **`auth.js`** (atau `auth.ts` jika menggunakan TypeScript), isi dengan kode implementasi setup berikut:

```javascript
// stores/auth.js
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

// defineStore menerima parameter 1: ID Unik Store, parameter 2: Callback Setup
export const useAuthStore = defineStore('auth', () => {
    
    // ==========================================
    // 1. STATE (Variabel Penyimpan Data)
    // ==========================================
    const token = ref(null);
    const user = ref(null); // Menampung info { nama, role, region }
    const isLoading = ref(false);

    // ==========================================
    // 2. GETTERS (Properti Terhitung / Computed)
    // ==========================================
    // Menghasilkan boolean apakah user sedang aktif login atau tidak
    const isLoggedIn = computed(() => token.value !== null);
    
    // Mendapatkan inisial nama dokter/petugas (contoh: "Ahmad Syarif" -> "AS")
    const inisialUser = computed(() => {
        if (!user.value || !user.value.nama) return '??';
        return user.value.nama
            .split(' ')
            .map(kata => kata[0])
            .join('')
            .toUpperCase()
            .substring(0, 2);
    });

    // ==========================================
    // 3. ACTIONS (Fungsi Logika / Pemanggilan API)
    // ==========================================
    
    // A. Fungsi Login memanggil server backend Express
    const loginPetugas = async (username, password) => {
        isLoading.value = true;
        try {
            // Simulasi fetch API ke server backend Express
            const respon = await $fetch('https://pmi-backend-api.onrender.com/api/auth/login', {
                method: 'POST',
                body: { username, password }
            });

            // Jika sukses, simpan token & info profil ke state
            token.value = respon.token;
            user.value = respon.user; // respon.user berisi { nama: '...', role: '...' }
            
            return { sukses: true };
        } catch (err) {
            // Bersihkan data jika gagal login
            token.value = null;
            user.value = null;
            return { sukses: false, pesan: err.message || 'Gagal terhubung ke server UDD.' };
        } finally {
            isLoading.value = false;
        }
    };

    // B. Fungsi Logout
    const logoutPetugas = () => {
        token.value = null;
        user.value = null;
        navigateTo('/login'); // Redirect instan ke halaman login
    };

    // Wajib kembalikan semua state, getters, dan actions agar bisa diakses komponen luar
    return {
        token,
        user,
        isLoading,
        isLoggedIn,
        inisialUser,
        loginPetugas,
        logoutPetugas
    };
});
```

---

## 3. Cara Menggunakan Store di Halaman Berkas Lain

Karena Nuxt 3 menggunakan fitur Auto-Imports, kita tidak perlu mengimpor berkas `useAuthStore` secara manual di file halaman. Kita bisa langsung memanggil instansinya:

```html
<!-- pages/login.vue -->
<template>
  <div class="space-y-4 max-w-xs mx-auto">
    <h3 class="font-bold text-slate-800">Login UDD</h3>
    
    <input type="text" v-model="username" placeholder="Username" class="w-full px-3 py-2 border rounded-xl" />
    <input type="password" v-model="password" placeholder="Password" class="w-full px-3 py-2 border rounded-xl" />
    
    <!-- Memanggil state loading dari store -->
    <button 
      @click="triggerLogin" 
      :disabled="auth.isLoading"
      class="w-full bg-red-500 text-white font-bold py-2 rounded-xl text-xs disabled:bg-slate-300"
    >
      {{ auth.isLoading ? '⏳ Memproses...' : 'Masuk Dashboard' }}
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const username = ref('');
const password = ref('');

// Inisialisasi Store Pinia secara instan (Auto-Imported)
const auth = useAuthStore();

const triggerLogin = async () => {
  const hasil = await auth.loginPetugas(username.value, password.value);
  if (hasil.sukses) {
    // Pindahkan ke dashboard jika sukses
    await navigateTo('/');
  } else {
    alert(`Gagal: ${hasil.pesan}`);
  }
};
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati bagaimana fungsi `inisialUser` dihitung menggunakan `computed()` di atas.
2. Jelaskan langkah pemrosesan metode JavaScript `.split(' ').map(...).join('')` pada computed tersebut dan berikan contoh outputnya jika nama petugas yang login adalah *"dr. Ahmad Syarif"*.
3. Mengapa kita wajib menuliskan perintah `return { ... }` di bagian paling bawah fungsi setup `defineStore` di Pinia?

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Analisis String computed inisialUser
Langkah eksekusi string nama *"dr. Ahmad Syarif"*:
1.  `.split(' ')`: Memecah teks kalimat berdasarkan spasi menjadi array kata: `["dr.", "Ahmad", "Syarif"]`.
2.  `.map(kata => kata[0])`: Mengambil huruf indeks ke-0 (huruf pertama) dari masing-masing kata di array: `["d", "A", "S"]`.
3.  `.join('')`: Menggabungkan kembali array huruf menjadi satu string: `"dAS"`.
4.  `.toUpperCase()`: Mengubah menjadi huruf kapital semua: `"DAS"`.
5.  `.substring(0, 2)`: Memotong string dan hanya mengambil 2 karakter pertama saja: **`"DA"`**.
*Hasilnya*: Teks inisial yang dicetak di lingkaran avatar profil adalah **`DA`**.

### Jawaban 3: Pentingnya Return Statement di Pinia
*   **Alasan**: Sintaks Setup Store Pinia bekerja persis seperti fungsi penutup Javascript biasa (*Javascript Closure*). Segala variabel dan fungsi yang dideklarasikan di dalam kurung kurawal bersifat privat (terisolasi di dalam file tersebut). 
*   Agar file halaman luar (seperti `login.vue` atau `index.vue`) bisa melihat, memodifikasi, dan memanggil fungsi `loginPetugas` atau properti `token`, kita wajib mengeksposnya keluar secara resmi lewat objek return di baris akhir store.
