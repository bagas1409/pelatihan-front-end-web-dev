# Hari 24: Komunikasi Anak ke Induk - Menggunakan Emits
## Memicu Fungsi Aksi di Komponen Induk dari Klik Tombol Komponen Anak Menggunakan defineEmits

Selamat datang di Hari 24! Hari ini kita akan mempelajari arah komunikasi sebaliknya: bagaimana komponen anak mengirimkan sinyal pemberitahuan atau memicu aksi di komponen induk menggunakan fungsi makro **`defineEmits`** di Vue 3.

Jika Props bertugas mengalirkan data ke bawah (Induk $\rightarrow$ Anak), maka Emits bertugas membisikkan sinyal ke atas (Anak $\rightarrow$ Induk). Di portal DonorKu, kita butuh membuat modal pop-up detail relawan (komponen anak) yang jika tombol "Tutup" di dalam modal tersebut diklik, dia memberi tahu halaman utama (komponen induk) untuk menutup modal tersebut dari layar.

---

## 1. Aliran Komunikasi Naik ke Atas (Events Up)

### 💡 Analogi Dunia Nyata: "Tombol Bell Panggilan Darurat Kamar Pasien UDD"
*   **Komponen Induk (Pusat Monitor Suster Jaga)**: Memiliki saklar utama untuk menyalakan lampu sirine atau memicu alarm bantuan dokter.
*   **Komponen Anak (Tombol Bell Fisik di Samping Ranjang Pasien)**: Pasien tidak memiliki hak atau kekuatan langsung untuk menyalakan sirine di lorong rumah sakit secara mandiri. Pasien hanya dibekali satu kabel tombol bell kecil di tangannya.
*   **Alurnya**: Saat pasien terbentur darurat, dia menekan tombol bell (`@click`). Tombol mengirimkan sinyal gelombang listrik naik ke atas (**Emit Event**). Begitu pusat monitor menangkap sinyal bel dari kamar 3, pusat monitor baru menyalakan sirine dan memanggil dokter jaga.

---

## 2. Mendefinisikan Sinyal Pancar dengan `defineEmits`

Di komponen anak, kita mendaftarkan daftar sinyal apa saja yang siap kita pancarkan ke atas menggunakan fungsi makro **`defineEmits`** di dalam script setup.

```html
<!-- components/ModalCitoConfirm.vue (Komponen Anak) -->
<template>
  <div class="fixed inset-0 bg-slate-900/60 flex items-center justify-center p-4 z-50">
    <div class="bg-white p-6 rounded-3xl max-w-sm w-full space-y-4">
      <h3 class="font-bold text-slate-900 text-base">🚨 Konfirmasi Darurat CITO</h3>
      <p class="text-sm text-slate-500">
        Apakah Anda yakin ingin memicu sirene darurat CITO untuk donor darah golongan ini?
      </p>
      
      <!-- Tombol Aksi -->
      <div class="flex gap-3 justify-end pt-2">
        <button 
          @click="pancarkanBatal" 
          class="px-4 py-2 text-xs font-semibold text-slate-500 hover:bg-slate-100 rounded-xl"
        >
          Batal
        </button>
        <button 
          @click="pancarkanSetuju" 
          class="px-4 py-2 text-xs font-bold text-white bg-red-500 hover:bg-red-600 rounded-xl"
        >
          Ya, Pemicu CITO
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
// Daftarkan nama sinyal event yang bisa dipancarkan ke induk
const emit = defineEmits(['tutup-modal', 'setujui-cito']);

const pancarkanBatal = () => {
  // Pancarkan sinyal 'tutup-modal' tanpa membawa data tambahan
  emit('tutup-modal');
};

const pancarkanSetuju = () => {
  // Pancarkan sinyal 'setujui-cito' dan bawa parameter data region Lampung
  emit('setujui-cito', { region: 'Bandar Lampung', timestamp: Date.now() });
};
</script>
```

---

## 3. Menangkap Sinyal Emit di Komponen Induk

Di komponen induk (`pages/index.vue`), kita mendengarkan pancaran sinyal dari anak menggunakan simbol `@` diikuti nama event pancarannya, sama seperti menangkap event klik tombol bawaan browser.

```html
<!-- pages/index.vue (Komponen Induk) -->
<template>
  <div class="p-8 text-center">
    <button 
      @click="showModal = true" 
      class="bg-red-500 text-white font-bold px-6 py-3 rounded-xl"
    >
      Picu Peringatan UDD
    </button>

    <!-- 
      Dengarkan sinyal dari anak:
      @tutup-modal -> mematikan status showModal
      @setujui-cito -> memicu fungsi eksekusiAPI dengan menangkap data kiriman anak
    -->
    <ModalCitoConfirm 
      v-if="showModal"
      @tutup-modal="showModal = false"
      @setujui-cito="eksekusiAPI"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue';

const showModal = ref(false);

// Menangkap parameter kiriman data dari anak
const eksekusiAPI = (payloadData) => {
  console.log("Sinyal diterima induk!");
  console.log(`Region CITO: ${payloadData.region}`);
  
  // Tutup modal kembali setelah aksi disetujui
  showModal.value = false;
};
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati alur penulisan `@setujui-cito="eksekusiAPI"` di atas.
2. Jelaskan bagaimana data `{ region: 'Bandar Lampung' }` yang dikirim dari file anak (`emit('setujui-cito', ...)`) bisa ditangkap oleh parameter `payloadData` di fungsi `eksekusiAPI` induk secara otomatis tanpa menulis parameter di tag HTML.
3. Sebutkan aturan penulisan (Naming Convention) yang direkomendasikan untuk menamai event pancaran emit di HTML vs di JavaScript setup script.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pengiriman Data Melalui Emit
*   Saat fungsi `emit('nama-event', dataPayload)` dipanggil di anak, Vue di belakang layar menangkap objek dataPayload tersebut sebagai argumen rahasia.
*   Jika di tag HTML induk kita hanya menuliskan nama fungsi referensi saja (`@setujui-cito="eksekusiAPI"` tanpa kurung tutup `()`), Vue secara otomatis akan menyuntikkan dataPayload tersebut sebagai parameter pertama masuk ke dalam fungsi `eksekusiAPI` tersebut. Ini mempermudah pengiriman detail data (seperti ID baris tabel) dari anak ke induk.

### Jawaban 3: Aturan Penamaan Emit (Naming Convention)
*   **Di JavaScript setup (`defineEmits`)**: Disarankan menggunakan format **camelCase** (contoh: `emit('closeModal')` atau `emit('submitForm')`).
*   **Di HTML Template (`@...`)**: Karena HTML bersifat case-insensitive (tidak membedakan huruf besar-kecil), disarankan mendengarkan event menggunakan format **kebab-case** (menggunakan tanda hubung strip, contoh: `@close-modal` atau `@submit-form`). Vue secara cerdas menerjemahkan camelCase di JS menjadi kebab-case di HTML secara otomatis.
