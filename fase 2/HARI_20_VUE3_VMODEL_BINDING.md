# Hari 20: Pengikatan Data Dua Arah (v-model)
## Mengikat Kolom Input Formulir dengan Variabel State Secara Instan

Selamat datang di Hari 20! Hari ini kita akan mempelajari teknik **Two-Way Data Binding** (Pengikatan Data Dua Arah) menggunakan direktif **`v-model`** di Vue 3.

Saat membuat form pendaftaran donor darah atau form tambah stok UDD, kita butuh sinkronisasi terus-menerus: apa yang diketik pengguna di layar langsung masuk ke memori JS, dan jika memori JS diubah secara internal oleh server, ketikan di layar ikut terisi otomatis. Di Vanilla JS atau React, kita harus menulis event handler manual seperti `onChange` dan `value` secara terpisah. Di Vue, direktif `v-model` mengurus semuanya secara otomatis dalam 1 kata kunci.

---

## 1. Konsep Pengikatan Data Dua Arah (Two-Way Binding)

### 💡 Analogi Dunia Nyata: "Boneka Tali Ventrilokuis & Tali Tarik Dua Arah"
Bayangkan Anda adalah dalang boneka kayu pertunjukan (developer) yang memegang tali boneka (variabel state).
*   **One-Way Binding (Tali Satu Arah)**: Anda menarik tali ke atas, tangan boneka ikut terangkat ke atas. Namun, jika ada penonton memukul tangan boneka tersebut hingga turun, tali di jari Anda tidak ikut tertarik bergerak. (Tali searah dari memori JS ke layar HTML).
*   **Two-Way Binding (Tali Ganda Dua Arah)**: Tali dihubungkan menggunakan katrol timbal balik. Jika Anda menarik tali, tangan boneka bergerak. Dan jika tangan boneka digerakkan paksa oleh penonton (pengguna mengetik di keyboard), jari Anda di atas ikut tertarik merasakan ketukan gerakan tersebut secara real-time.

---

## 2. Struktur Sintaks v-model

Direktif `v-model` secara cerdas menyesuaikan perilakunya tergantung jenis inputan HTML yang ditempeli:

*   **Teks Input / Textarea**: Mengikat properti `value` dan memantau event `input`.
*   **Checkbox / Radio Button**: Mengikat properti `checked` dan memantau event `change`.
*   **Select Dropdown**: Mengikat properti `value` seleksi pilihan.

---

## 3. Studi Kasus PMI: Formulir Deteksi Kelayakan Donor

Mari kita buat komponen Vue 3 dinamis untuk memeriksa kelayakan donor darah berdasarkan syarat minimal **berat badan 45 Kg**.

```html
<!-- components/FormKelayakan.vue -->
<template>
  <div class="max-w-md mx-auto p-6 bg-white rounded-2xl border border-slate-200 shadow-sm mt-8 space-y-6">
    <h3 class="text-lg font-bold text-slate-800 border-b pb-3">Pemeriksaan Awal Relawan</h3>

    <!-- 1. INPUT TEKS: NAMA -->
    <div>
      <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Nama Relawan</label>
      <!-- v-model mengikat string nama -->
      <input 
        type="text" 
        v-model="nama" 
        class="w-full px-4 py-2 border rounded-xl focus:ring focus:ring-red-100 focus:border-red-500 outline-none"
        placeholder="Nama Lengkap"
      />
    </div>

    <!-- 2. INPUT ANGKA: BERAT BADAN -->
    <div>
      <label class="block text-xs font-bold text-slate-500 uppercase mb-2">Berat Badan (Kg)</label>
      <!-- .number memaksa string ketikan otomatis diconvert menjadi angka asli -->
      <input 
        type="number" 
        v-model.number="berat" 
        class="w-full px-4 py-2 border rounded-xl focus:ring focus:ring-red-100 focus:border-red-500 outline-none"
        placeholder="Contoh: 50"
      />
    </div>

    <!-- 3. VISUALISASI REAKTIF -->
    <div class="p-4 rounded-xl text-sm" :class="berat >= 45 ? 'bg-emerald-50 text-emerald-800' : 'bg-red-50 text-red-800'">
      <p>Halo <strong>{{ nama || '...' }}</strong>,</p>
      <p class="mt-1" v-if="berat >= 45">
        ✓ Berat badan Anda memadai. Anda diperbolehkan mendaftar donor darah.
      </p>
      <p class="mt-1" v-else>
        🚨 Maaf, berat badan minimal untuk donor darah adalah 45 Kg.
      </p>
    </div>

    <!-- Tombol Reset Cepat (Membuktikan data mengalir balik dari JS ke Layar) -->
    <button 
      @click="resetForm" 
      class="w-full py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 font-semibold rounded-xl text-xs transition-colors"
    >
      Reset Formulir
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// Deklarasikan variabel awal
const nama = ref('');
const berat = ref(0);

// Fungsi reset mengosongkan state JS. Inputan di layar otomatis ikut kosong!
const resetForm = () => {
  nama.value = '';
  berat.value = 0;
};
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati atribut `v-model.number="berat"` pada formulir di atas.
2. Jelaskan kegunaan modifier kustom **`.number`** pada direktif `v-model` tersebut dan apa akibatnya bagi perhitungan matematika di JavaScript jika kita tidak menggunakannya.
3. Sebutkan modifier kustom bawaan `v-model` lainnya di Vue 3 beserta fungsi kegunaannya masing-masing. (Sebutkan minimal 2).

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kegunaan Modifier `.number`
*   Secara standar web, semua inputan dari tag `<input>` (meskipun bertipe `type="number"`) akan diterima oleh JavaScript dalam bentuk tipe data **String** (contoh: `"55"`).
*   Jika kita tidak menggunakan modifier `.number`, saat kita melakukan operasi matematika seperti penjumlahan: `berat.value + 5`, hasilnya akan berupa penggabungan string teks yang aneh yaitu **`"555"`**, bukan angka **`60`**. Properti `.number` memaksa Vue mengonversi ketikan tersebut ke dalam tipe data Float/Integer asli sebelum masuk ke state memori.

### Jawaban 3: Modifier v-model Lainnya:
1.  **`.trim`**: Berfungsi memotong/menghapus spasi kosong berlebih secara otomatis di awal dan akhir ketikan pengguna (sangat berguna untuk input email/username agar terhindar dari spasi tidak sengaja).
2.  **`.lazy`**: Berfungsi menunda sinkronisasi reaktivitas data. Secara default, `v-model` memperbarui state di setiap ketukan keyboard (*input event*). Dengan `.lazy`, data baru akan disinkronisasikan ke memori JS setelah pengguna menekan tombol Enter atau memindahkan kursor fokus keluar dari kolom teks tersebut (*change event*).
