# Hari 22: Event Handling & Event Modifiers
## Mencegah Refresh Halaman Otomatis Menggunakan Modifier @submit.prevent

Selamat datang di Hari 22! Hari ini kita akan mempelajari cara menangkap dan mengontrol interaksi tombol/form menggunakan event listener bawaan Vue: **`v-on`** (yang disingkat menjadi simbol **`@`**) serta memanfaatkan **Event Modifiers** untuk menyederhanakan kode JavaScript kita.

Salah satu penderitaan terbesar saat memproses formulir pendaftaran di web tradisional adalah mencegah halaman web memuat ulang (*refresh*) secara tidak sengaja saat tombol submit ditekan. Vue mempermudah hal tersebut dengan menyediakan pintasan deklaratif langsung di tag HTML.

---

## 1. Menangkap Event dengan @click dan @submit

Di Vue 3, untuk mendengarkan aktivitas interaksi pengguna, kita menggunakan simbol `@` diikuti dengan nama eventnya:
*   `@click="..."` $\rightarrow$ Mendengarkan ketukan klik mouse/layar.
*   `@submit="..."` $\rightarrow$ Mendengarkan pengiriman formulir.
*   `@keyup.enter="..."` $\rightarrow$ Mendengarkan ketukan tombol keyboard khusus Enter.

---

## 2. Mengenal Keajaiban Event Modifiers
Di JavaScript biasa, saat memproses event submit form, kita wajib menuliskan baris `event.preventDefault()` agar halaman web tidak berkedip menyegarkan diri yang menghapus semua data RAM kita.

Vue menyediakan modifier cerdas yang ditempel langsung menggunakan tanda titik (`.`) di belakang nama event:
*   **`.prevent`**: Memicu fungsi `event.preventDefault()` secara otomatis di balik layar sebelum memanggil fungsi kita.
*   **`.stop`**: Memicu `event.stopPropagation()` untuk menghentikan efek riak klik merembet ke kontainer luar (*Event Bubbling*).
*   **`.once`**: Memastikan fungsi klik hanya boleh terpicu **satu kali saja** (mencegah relawan menekan tombol submit berkali-kali secara brutal yang bisa memicu data ganda di database).

---

## 3. Studi Kasus PMI: Formulir Tambah Stok Darah UDD

Mari kita buat komponen form interaktif untuk menambah persediaan kantong darah UDD Lampung menggunakan event modifiers yang aman dari double-submit.

```html
<template>
  <div class="max-w-md mx-auto p-8 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-6">
    <h3 class="font-bold text-slate-800 text-base">Registrasi Masuk Kantong Darah</h3>

    <!-- 
      @submit.prevent -> Mencegah halaman refresh otomatis
    -->
    <form @submit.prevent="prosesSimpanStok" class="space-y-4">
      
      <!-- Kolom Golongan Darah -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Golongan Darah</label>
        <select v-model="golongan" class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500">
          <option value="A">A</option>
          <option value="B">B</option>
          <option value="AB">AB</option>
          <option value="O">O</option>
        </select>
      </div>

      <!-- Kolom Jumlah Kantong -->
      <div>
        <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Jumlah Kantong</label>
        <!-- @keyup.enter -> Jika ditekan Enter di input ini, otomatis submit form -->
        <input 
          type="number" 
          v-model.number="jumlah" 
          class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500"
          placeholder="Contoh: 10"
        />
      </div>

      <!-- Tombol Submit Kirim Data -->
      <!-- 
        :disabled -> Tombol mati saat loading sedang aktif
      -->
      <button 
        type="submit" 
        :disabled="isSubmitting"
        class="w-full bg-red-500 hover:bg-red-600 disabled:bg-slate-300 text-white font-bold py-3 rounded-xl transition-colors active:scale-[0.98] transform"
      >
        {{ isSubmitting ? '⌛ Menyimpan...' : 'Simpan Persediaan' }}
      </button>

    </form>

    <!-- Log Aktivitas Terkini -->
    <div v-if="pesanSukses" class="p-4 bg-emerald-50 text-emerald-800 rounded-xl text-xs font-medium">
      {{ pesanSukses }}
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const golongan = ref('O');
const jumlah = ref(0);
const isSubmitting = ref(false);
const pesanSukses = ref('');

const prosesSimpanStok = async () => {
  // Cegah submit ganda jika sedang loading
  if (isSubmitting.value) return;

  isSubmitting.value = true;
  pesanSukses.value = '';

  try {
    // Simulasi menembak API server tunda 1.5 detik
    await new Promise(resolve => setTimeout(resolve, 1500));

    pesanSukses.value = `✓ Sukses mencatat: Tambah ${jumlah.value} kantong Golongan ${golongan.value} ke database UDD.`;
    
    // Reset jumlah ke nol kembali setelah sukses
    jumlah.value = 0;
  } catch (err) {
    console.error("Gagal menyimpan data:", err.message);
  } finally {
    // Matikan loading state
    isSubmitting.value = false;
  }
};
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati kode tombol submit: `type="submit" :disabled="isSubmitting"`.
2. Jelaskan bahaya keamanan data logistik jika kita membiarkan tombol submit tetap aktif (`disabled="false"`) saat proses tunggu asinkron server API sedang berjalan lambat.
3. Sebutkan fungsi dari event modifier **`.stop`** di Vue 3 dan berikan contoh skenario penggunaannya.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Bahaya Double Submit
*   **Bahaya Data Duplikat**: Jika internet sedang lambat (loading memakan waktu 5 detik) dan tombol tidak di-disable, petugas admin cenderung panik/tidak sabar lalu mengklik tombol submit berkali-kali secara beruntun.
*   **Dampak pada Server**: Browser akan mengirimkan 5 request HTTP POST yang sama ke server secara bersamaan. Server backend yang tidak memiliki sistem proteksi ganda akan memproses kelima request tersebut dan memasukkan data stok sebanyak 5 kali berturut-turut. Hal ini memicu data sampah di database logistik UDD PMI (*Double Insertion Bug*).

### Jawaban 3: Kegunaan Modifier `.stop`
*   **Fungsi `.stop`**: Mencegah terjadinya rambatan event klik ke kontainer induk terluar (*Event Bubbling*).
*   **Skenario Penggunaan**: Bayangkan Anda membuat kartu daftar logistik yang jika diklik akan membuka halaman detail kartu. Di dalam kartu tersebut, ada tombol kecil bertuliskan **"Hapus"**.
    *   Jika tombol Hapus diklik tanpa modifier `.stop` (`@click.stop="hapusData"`), browser akan memicu fungsi hapus data sekaligus **tidak sengaja memicu fungsi pembukaan detail kartu** karena klik merambat keluar. Modifier `.stop` membatasi klik hanya terhenti di area tombol kecil tersebut saja.
