# Hari 68: Sinkronisasi Data Real-time (Polling Engine)
## Memantau Lonceng Notifikasi Darurat CITO Menggunakan Polling Interval Setiap 10 Detik

Selamat datang di Hari 68! Hari ini kita akan mempelajari teknik sinkronisasi data real-time tingkat dasar: **Polling Engine** (Mesin Polling Interval) di Nuxt 3.

Pada portal UDD PMI Lampung, lonceng notifikasi panggilan darurat CITO (stok kritis) harus selalu update tanpa mengharuskan petugas menekan tombol F5 refresh secara manual. Sebelum kita mempelajari teknologi Websocket yang rumit di masa depan, kita akan menggunakan teknik **Short Polling** yaitu menyuruh browser secara berkala menembak API server (`GET /api/permintaan/cito`) setiap 10 detik di latar belakang guna mendeteksi ada tidaknya pasien kritis baru.

---

## 1. Konsep Short Polling & Pembersihan Siklus

*   **Cara Kerja**: Kita menyalakan timer `setInterval` saat halaman dimuat (`onMounted`) untuk memicu eksekusi API secara berkala.
*   **Peringatan Keras**: Kita **wajib** membunuh timer tersebut menggunakan `clearInterval` saat petugas keluar meninggalkan halaman (`onUnmounted`) guna mencegah terjadinya kebocoran memori RAM (*Memory Leak*) yang sudah dipelajari di Hari 58.

---

## 2. Studi Kasus PMI: Polling Peringatan Darurat CITO

Mari kita buat komponen notifikasi cito pintar yang melakukan polling data ke server backend setiap 10 detik.

```html
<!-- components/dashboard/LoncengCito.vue -->
<template>
  <div class="relative">
    <!-- Icon Lonceng Bel -->
    <button 
      @click="toggleDropdown" 
      class="p-2 hover:bg-slate-100 rounded-xl relative text-slate-600 transition-colors"
    >
      🔔
      <!-- Lampu Merah Berkedip jika ada kasus CITO aktif -->
      <span 
        v-if="jumlahCitoAktif > 0" 
        class="absolute top-1 right-1 w-2.5 h-2.5 bg-red-500 rounded-full animate-ping"
      ></span>
      <span 
        v-if="jumlahCitoAktif > 0" 
        class="absolute top-1 right-1 w-2.5 h-2.5 bg-red-500 rounded-full"
      ></span>
    </button>

    <!-- Dropdown List Alert CITO -->
    <div 
      v-if="isOpen && listCito.length > 0" 
      class="absolute right-0 mt-2 w-72 bg-white rounded-2xl border border-slate-200 shadow-lg p-4 space-y-3 z-50 text-xs"
    >
      <h4 class="font-bold text-red-500 border-b pb-2">🚨 Panggilan Darurat CITO ({{ jumlahCitoAktif }})</h4>
      
      <ul class="divide-y divide-slate-100 max-h-48 overflow-y-auto">
        <li v-for="item in listCito" :key="item.id" class="py-2.5 space-y-1">
          <div class="flex justify-between font-bold text-slate-800">
            <span>Golongan {{ item.golongan }}</span>
            <span class="text-red-500">Butuh {{ item.jumlah }} Bag</span>
          </div>
          <p class="text-slate-400">Target RS: {{ item.rumahSakit }}</p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue';

const isOpen = ref(false);
const listCito = ref([]);
let intervalId = null;

const jumlahCitoAktif = computed(() => listCito.value.length);

const toggleDropdown = () => {
  isOpen.value = !isOpen.value;
};

// 1. FUNGSI PENEMBAK API (DIJALANKAN DI LATAR BELAKANG)
const periksaCitoBaru = async () => {
  console.log("[Polling Engine] Memeriksa status darurat ke server backend...");
  
  try {
    // Tembak API Cito (Ganti dengan endpoint API asli backend Anda)
    const data = await $fetch('https://pmi-backend-api.onrender.com/api/donations');
    
    // Saring data simulasi yang berstatus kritis
    listCito.value = data.slice(0, 2).map((item, index) => ({
      id: item.id || index,
      golongan: index % 2 === 0 ? 'O' : 'AB',
      jumlah: 3 + index,
      rumahSakit: item.tempat || 'RS Swasta Metro'
    }));

  } catch (err) {
    console.error("Gagal polling data CITO:", err.message);
  }
};

// 2. LIFECYCLE MOUNTED: Nyalakan mesin polling
onMounted(() => {
  // Jalankan pemeriksaan pertama kali saat halaman dibuka
  periksaCitoBaru();

  // Setel interval berputar setiap 10.000 milidetik (10 detik)
  intervalId = setInterval(periksaCitoBaru, 10000);
});

// 3. LIFECYCLE UNMOUNTED: Matikan mesin polling
onUnmounted(() => {
  console.log("[Polling Engine] Mematikan interval polling. RAM bersih.");
  if (intervalId) {
    clearInterval(intervalId); // BUNUH ENGINE UNTUK MENCEGAH RELEASING MEMORY LEAK!
  }
});
</script>
```

---

## 3. Latihan Soal Mandiri
1. Amati pengaturan waktu interval: `setInterval(periksaCitoBaru, 10000)`.
2. Jelaskan bahaya keamanan bagi server backend API jika kita menyetel durasi waktu polling terlalu cepat (misalnya polling setiap 100 milidetik / 10 kali dalam satu detik) dan dikunjungi oleh 1.000 petugas PMI Lampung secara bersamaan.
3. Sebutkan kelemahan teknologi Short Polling dibandingkan dengan teknologi **WebSockets (Server-Sent Events)** untuk kebutuhan data yang menuntut status instan real-time dalam milidetik.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Bahaya DDoS Akibat Polling Terlalu Cepat
*   **Bahaya Overload Server (Self-inflicted DDoS)**: Jika 1.000 petugas membuka dashboard, dan browser masing-masing menembak API 10 kali per detik, server backend akan menerima **10.000 request HTTP per detik** secara terus menerus tanpa henti.
*   **Dampaknya**: CPU server backend akan menyentuh angka 100%, server memanas, database terkunci (*Database Lock*), dan akhirnya website UDD PMI akan padam crash tumbang total (*Server Crash Down*) akibat overload request yang kita buat sendiri. Durasi polling 10 s.d 30 detik adalah batas aman yang direkomendasikan untuk short polling.

### Jawaban 3: Kelemahan Polling vs WebSockets
*   **Pemborosan Bandwidth (Header Overhead)**: Setiap kali polling dikirim, browser harus mengirim paket header HTTP lengkap (termasuk cookie, authorization token JWT, metadata). Jika tidak ada kasus kritis baru di server, server mengembalikan data kosong. Kita membuang bandwidth internet untuk hal sia-sia.
*   **Tidak Real-time Sejati**: Jika ada pasien darurat didaftarkan di detik ke-1, petugas baru akan melihat peringatan di layar monitor setelah menunggu 9 detik berikutnya hingga siklus interval polling browser berputar kembali. WebSockets menyelesaikan ini dengan membuat koneksi satu pipa terbuka penuh di mana server bisa langsung mendorong (*Push*) data secara instan dalam 1 milidetik ke browser tanpa menunggu browser meminta.
