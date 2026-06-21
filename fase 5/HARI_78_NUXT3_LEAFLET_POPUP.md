# Hari 78: Interaksi Balon Informasi Peta (Map Popups)
## Menampilkan Kotak Info Persediaan Darah UDD Saat Marker Peta Diklik

Selamat datang di Hari 78! Hari ini kita akan mempelajari implementasi **Popup Interaktif** (Balon Keterangan) di atas peta Leaflet.js.

Menampilkan titik marker saja di peta belum cukup informatif. Kita ingin agar ketika pimpinan PMI mengklik ikon salah satu Rumah Sakit Mitra di peta, muncul balon dialog melayang (*Popup balloon*) yang memuat informasi detail riil seperti nama rumah sakit, nomor telepon darurat, status kebutuhan golongan darah, dan tombol jalan pintas untuk memproses logistik pengiriman darah.

---

## 1. Konsep HTML Popup di Leaflet

Leaflet memiliki fitur **`.bindPopup()`** yang bertugas menempelkan event listener klik pada marker.
*   Keunggulan Leaflet: Kita bisa menuliskan **Tag HTML murni** di dalam isi parameter bindPopup (seperti tag `<b>`, `<div>`, hingga class CSS Tailwind). Ini memungkinkan kita merancang desain isi popup yang sangat premium.

---

## 2. Studi Kasus PMI: Menempelkan Info Logistik Darurat di Popup RS

Mari kita modifikasi rute penggambaran marker kita agar menyertakan fungsi bindPopup HTML dinamis.

Buka kembali berkas halaman **`pages/peta/index.vue`** Anda, lalu sesuaikan kode foreach di bagian mounting:

```javascript
// ... kode inisialisasi mapInstance dan L.icon tetap sama seperti Hari 77 ...

// Data koordinat lengkap dengan informasi logistik
const daftarKordinat = [
  { 
    nama: 'UDD PMI Lampung Pusat', 
    lat: -5.397, 
    lng: 105.266, 
    tipe: 'PMI',
    detail: 'Stok Kantong Darah Aman (✓ 342 Bag)'
  },
  { 
    nama: 'RS Urip Sumoharjo', 
    lat: -5.385, 
    lng: 105.285, 
    tipe: 'RS',
    detail: 'Butuh Segera: <b>5 Bag Darah O-</b>'
  },
  { 
    nama: 'RSU Abdul Moeloek', 
    lat: -5.405, 
    lng: 105.245, 
    tipe: 'RS',
    detail: 'Butuh Segera: <b>3 Bag Darah AB+</b>'
  }
];

// Loop menggambar marker dengan popup HTML kustom
daftarKordinat.forEach(titik => {
  const ikonPilihan = titik.tipe === 'PMI' ? ikonPmi : ikonRs;

  // Rancang template HTML untuk isi popup balon
  const htmlPopup = `
    <div class="p-2 space-y-2 text-xs font-sans">
      <div class="flex justify-between items-center border-b pb-1 border-slate-100">
        <strong class="text-slate-800 font-bold block">${titik.nama}</strong>
      </div>
      <p class="text-slate-500 leading-normal">${titik.detail}</p>
      
      <!-- Tombol Aksi di dalam Map Popup -->
      <a 
        href="/cito" 
        class="block text-center bg-red-500 text-white font-bold py-1.5 rounded-lg text-[9px] uppercase tracking-wider no-underline hover:bg-red-600 transition-colors mt-2"
      >
        Proses Logistik
      </a>
    </div>
  `;

  // Gambar marker dan ikat balon popup ke marker tersebut
  L.marker([titik.lat, titik.lng], { icon: ikonPilihan })
   .bindPopup(htmlPopup) // Tempelkan HTML popup di sini!
   .addTo(mapInstance);
});
```

Coba jalankan server dev Anda, buka halaman peta, lalu klik ikon Rumah Sakit Abdul Moeloek. Anda akan melihat balon putih meluncur dari kepala marker menampilkan informasi detail logistik lengkap dengan tombol link merah yang fungsional!

---

## 3. Latihan Soal Mandiri
1. Amati kode template HTML pada variabel `htmlPopup` di atas.
2. Jelaskan fungsi dari tag `<a>` dengan properti `class="no-underline"` yang dipasang di dalam popup di atas.
3. Sebutkan kelemahan jika kita menggunakan fungsi JavaScript reaktif Vue (seperti mengklik tombol pemicu `@click="proses"`) langsung di dalam template string HTML Leaflet `bindPopup` di atas, dan jelaskan solusi alternatifnya.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pengaturan Tag Link pada Leaflet Popup
*   **Fungsi `no-underline`**: Menghapus garis bawah biru bawaan browser pada tautan link `<a>`, menyelaraskan desain tombol agar bersih bernuansa premium.
*   **Target Navigasi**: Saat diklik, browser akan langsung mengalihkan rute halaman masuk ke portal kelola darurat `/cito` untuk segera memproses pengiriman kantong darah ke RS tersebut.

### Jawaban 3: Masalah Reaktivitas Vue di Luar Sistem (Leaflet DOM)
*   **Kelemahan**: Leaflet merender string `htmlPopup` secara manual langsung ke DOM browser di luar sistem pengawasan virtual DOM Vue. Akibatnya, arahan reaktif Vue 3 seperti `@click`, `v-model`, atau `v-if` **tidak akan berfungsi sama sekali** dan dianggap sebagai teks mati biasa oleh browser.
*   **Solusi Alternatif**: Jika membutuhkan interaksi reaktif Vue yang kompleks di dalam popup, kita disarankan membuat marker Leaflet polos, lalu mendengarkan event klik marker `marker.on('click', ...)` untuk kemudian menyalakan komponen modal dialog laci bawah (*Bottom Sheet*) reaktif buatan Vue asli di luar peta.
