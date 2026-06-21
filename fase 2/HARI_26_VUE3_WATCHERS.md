# Hari 26: Watchers - Memantau Perubahan Data
## Menggunakan watch() & watchEffect() Untuk Memicu Aksi Filter Wilayah

Selamat datang di Hari 26! Hari ini kita akan mempelajari teknik pemantauan perubahan data menggunakan **Watchers** via fungsi **`watch()`** dan **`watchEffect()`** di Vue 3.

Jika Computed Property bertugas menghasilkan nilai terhitung baru dari data lama secara sinkron, maka Watchers bertugas mendeteksi perubahan data untuk **memicu efek samping (Side-Effects) asinkron**, seperti mengirim log aktivitas ke server, menyimpan data ke localStorage, atau memicu request pencarian API wilayah ketika petugas mengetik di filter pencarian.

---

## 1. Perbedaan Utama: computed() vs watch()

*   **`computed()`**: *"Tolong hitungkan total stok darah dari array ini dan berikan saya hasilnya."* (Harus mengembalikan nilai / *Return Value*, dan tidak boleh melakukan request asinkron).
*   **`watch()`**: *"Tolong awasi input pencarian wilayah ini. Jika petugas mengetik nama kota baru, jalankan fungsi asinkron memanggil API server UDD untuk memfilter tabel."* (Tidak harus mengembalikan nilai, melainkan memicu rentetan fungsi sampingan).

---

## 2. Mengenal watch() vs watchEffect()

*   **`watch()` (Pengawas Spesifik / Manual)**:
    *   Kita harus menyebutkan secara eksplisit variabel mana yang ingin kita awasi.
    *   Menyediakan parameter nilai baru (`newValue`) dan nilai lama (`oldValue`) sebelum berubah.
    *   Secara default tidak berjalan saat pertama kali halaman dimuat (*lazy*), kecuali kita menyalakan opsi `{ immediate: true }`.
*   **`watchEffect()` (Pengawas Otomatis)**:
    *   Kita tidak perlu menuliskan nama variabel apa yang diawasi.
    *   Dia secara pintar menyisir seluruh isi baris fungsi di dalamnya, mendeteksi variabel reaktif apa saja yang dipanggil, lalu langsung mengawasinya secara otomatis.
    *   Langsung dieksekusi satu kali sejak awal halaman dimuat (*immediate*).

---

## 3. Studi Kasus PMI: Filter Pencarian Wilawan & Log Simpan Data

Mari kita buat fitur pencarian nama wilayah relawan yang dipantau oleh `watch()`, dan menyimpan status filter ke memori browser menggunakan `watchEffect()`.

```html
<template>
  <div class="max-w-md mx-auto p-6 bg-white rounded-3xl border border-slate-200 shadow-sm mt-8 space-y-4">
    <h3 class="font-bold text-slate-800 text-sm">Filter Relawan UDD</h3>

    <!-- Input Filter Wilayah -->
    <div>
      <label class="block text-[10px] font-bold text-slate-400 uppercase mb-2">Region Cabang</label>
      <select v-model="filterRegion" class="w-full px-4 py-2 border rounded-xl outline-none focus:border-red-500 text-sm">
        <option value="Semua">Semua Wilayah</option>
        <option value="Bandar Lampung">Bandar Lampung</option>
        <option value="Metro">Metro</option>
        <option value="Lamsel">Lampung Selatan</option>
      </select>
    </div>

    <!-- Status Loading Status -->
    <div v-if="isLoading" class="text-xs text-amber-600 animate-pulse">
      ⏳ Memfilter data relawan di database pusat...
    </div>

    <!-- Hasil Filter Rekap -->
    <div class="p-4 bg-slate-50 rounded-2xl text-xs space-y-1">
      <p>Region Aktif: <strong>{{ filterRegion }}</strong></p>
      <p class="text-slate-400">Total region terpilih diubah sebanyak: {{ hitungPerubahan }} kali</p>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, watchEffect } from 'vue';

const filterRegion = ref('Semua');
const isLoading = ref(false);
const hitungPerubahan = ref(0);

// 1. WATCH: Mengawasi variabel "filterRegion" secara spesifik
watch(filterRegion, async (wilayahBaru, wilayahLama) => {
  console.log(`Region berubah dari: "${wilayahLama}" menjadi: "${wilayahBaru}"`);
  
  // Nyalakan indikator loading
  isLoading.value = true;
  hitungPerubahan.value++;

  // Simulasi memicu API filter asinkron tunda 1 detik
  await new Promise(resolve => setTimeout(resolve, 1000));
  
  isLoading.value = false;
});

// 2. WATCHEFFECT: Berjalan otomatis memantau apapun variabel di dalamnya
watchEffect(() => {
  // watchEffect mendeteksi penggunaan variabel "filterRegion" di bawah
  console.log(`[watchEffect] Menyinkronkan pilihan "${filterRegion.value}" ke LocalStorage...`);
  
  // Simulasi menyimpan konfigurasi ke memori browser
  // localStorage.setItem('region_pref', filterRegion.value);
});
</script>
```

---

## 4. Latihan Soal Mandiri
1. Amati kode pemantauan `watch(filterRegion, ...)` di atas.
2. Mengapa pada fungsi `watch()` kita diberikan akses parameter nilai lama (`wilayahLama`), sedangkan di fungsi `watchEffect()` kita tidak bisa menangkap nilai parameter lama tersebut secara bawaan? Jelaskan konsep arsitekturnya.
3. Sebutkan parameter konfigurasi tambahan apa yang harus kita pasang di `watch()` agar pengawas bersedia mengecek perubahan objek bertipe *Nested Object* (objek bertingkat di dalam objek) secara mendalam.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Ketiadaan Nilai Lama di watchEffect
*   **watch()** adalah pengawas terarah (*State-Specific Tracker*). Karena Vue mengetahui secara pasti 1 variabel tunggal yang diawasi, Vue menyimpan salinan nilai lama variabel tersebut di RAM sebelum ditimpa, lalu menyuplainya ke parameter callback.
*   **watchEffect()** adalah pengawas efek samping (*Dependency-Effect Collector*). Tugas utamanya hanyalah menjalankan ulang fungsi di dalamnya kapanpun ada variabel dependensi di dalam fungsi tersebut yang mengalami mutasi. Karena dia bisa memantau 5 variabel sekaligus secara dinamis, Vue tidak bisa menyediakan parameter nilai lama secara spesifik untuk tiap variabel secara efisien.

### Jawaban 3: Opsi Deep Watcher
*   Untuk memantau perubahan properti di dalam objek bertingkat (Nested Object), kita harus menyertakan opsi **`{ deep: true }`** di parameter ketiga fungsi `watch`.
    ```javascript
    watch(profilRelawan, (baru) => {
      console.log("Properti di dalam profil berubah!");
    }, { deep: true });
    ```
    Tanpa opsi `deep: true`, Vue hanya akan mengawasi alamat memori objek terluar saja, dan akan mengabaikan perubahan jika hanya kolom kecil di dalam objek (misal: `profilRelawan.alamat.kota`) yang disunting pengguna.
