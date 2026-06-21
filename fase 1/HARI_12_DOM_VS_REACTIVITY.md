# Hari 12: DOM Manipulation vs Konsep Reaktivitas Data
## Memahami Mengapa Menulis Tampilan Manual Sangat Melelahkan, dan Mengapa Framework Modern Lahir

Selamat datang di Hari 12! Hari ini kita akan mempelajari pergeseran paradigma (cara berpikir) terpenting dari era web dinamis tradisional (menggunakan Vanilla JS/jQuery) ke era web modern berbasis **Reaktivitas Data** (seperti Vue dan React).

Materi ini adalah jembatan emas sebelum kita masuk ke Fase 2 menggunakan Nuxt 3. Kita akan menguji seberapa melelahkannya memperbarui tampilan layar secara manual jika ada data stok baru masuk, dan bagaimana konsep reaktivitas data menyelesaikan penderitaan menulis kode tersebut.

---

## 1. Era Lama: Manipulasi DOM Manual (Imperatif)

### 💡 Analogi Dunia Nyata: "Melukis Ulang Papan Tulis dengan Tangan"
Bayangkan Anda adalah petugas administrasi UDD PMI yang memegang kapur tulis.
*   **Cara Imperatif (Manipulasi DOM Manual)**: Setiap kali ada donor menyumbang 1 kantong darah O, Anda harus berjalan ke papan tulis, mengambil penghapus, menghapus tulisan angka lama, mengambil kapur, menulis angka baru, lalu memeriksa apakah warna tulisan sudah pas. Anda mengatur baris piksel layar satu per satu secara manual.
    *   *Kelemahan*: Jika data stok berubah di 5 tempat berbeda di layar web, Anda wajib menulis 5 baris kode instruksi pencarian elemen dan pembaruan teks secara berulang. Jika lupa satu saja, tampilan web akan selisih data dengan database.

---

## 2. Perbandingan Kode: Mengubah Angka Stok Darah di Layar

### Gaya Tradisional (Vanilla JS - Imperatif):
Di kode ini, kita harus mencari elemen id fisik di HTML terlebih dahulu, baru menyuntikkan nilainya.

```html
<!-- HTML -->
<div>Stok Golongan O: <span id="angka-stok">10</span> Kantong</div>
<button id="tombol-tambah">Tambah</button>

<!-- JavaScript Manual -->
<script>
  let stok = 10;
  const tombol = document.getElementById("tombol-tambah");
  const tampilanAngka = document.getElementById("angka-stok");

  tombol.addEventListener("click", () => {
    stok = stok + 1; // 1. Perbarui nilai data memori JS
    tampilanAngka.innerText = stok; // 2. Perbarui DOM visual layar secara manual! (Rawan lupa)
  });
</script>
```

---

### Gaya Modern (Reaktivitas Data - Deklaratif):
Di framework modern (seperti Vue/Nuxt), kita **tidak pernah menyentuh elemen HTML secara manual**. Kita cukup menyambungkan variabel state dengan teks layar, lalu merubah datanya. Layar otomatis tergambar ulang sendiri di latar belakang secara ajaib.

```html
<!-- Konsep Deklaratif (Vue 3) -->
<template>
  <div>
    <!-- Teks layar diikat langsung ke data 'stok' -->
    <p>Stok Golongan O: {{ stok }} Kantong</p>
    
    <!-- Klik tombol langsung memanipulasi data memori, bukan elemen HTML -->
    <button @click="stok++">Tambah</button>
  </div>
</template>

<script setup>
import { ref } from 'vue';
// Deklarasikan variabel reaktif
const stok = ref(10);
</script>
```

---

## 3. Mengapa Konsep Reaktivitas Sangat Revolusioner?

1.  **Deklaratif vs Imperatif**: 
    *   *Imperatif*: *"Browser, tolong cari elemen ID ini, ganti teksnya menjadi X, ubah warnanya menjadi merah."* (Bagaimana cara merubahnya).
    *   *Deklaratif*: *"Ini data stok saya, silakan gambar di layar. Jika data ini berubah, tolong sesuaikan sendiri."* (Apa hasil akhirnya).
2.  **Satu Sumber Kebenaran (Single Source of Truth)**: Data di memori JavaScript dan tampilan layar selalu 100% sinkron. Tidak akan pernah terjadi situasi di mana data stok di memori bernilai `11` namun layar masih menampilkan angka `10`.

---

## 4. Latihan Soal Mandiri
1. Analisis kode perbandingan Vanilla JS dengan Vue 3 di atas.
2. Jika sebuah halaman web memiliki 20 kartu stok darah dinamis yang datanya bisa di-update dari sensor internet setiap 3 detik, sebutkan kesulitan teknis apa saja yang akan dihadapi developer jika memaksakan menulis kodenya menggunakan Vanilla JS DOM manipulation murni.
3. Jelaskan konsep dari kata *Data Binding* di dalam sistem reaktivitas framework modern.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Tantangan Vanilla JS Skala Besar
Kesulitan memaksakan DOM manual untuk 20 kartu real-time:
*   **Kerumitan Kode (Spaghetti Code)**: Developer harus menulis minimal 20 baris perintah `document.getElementById` atau `querySelector` berbeda secara konsisten.
*   **Beban CPU Berat**: Menghapus dan menggambar ulang elemen HTML secara langsung di browser berulang-ulang tanpa algoritma pembanding akan membuat browser boros memori RAM dan tersendat (*lag*).
*   **Sangat Rawan Bug**: Jika ada satu penulisan ID elemen yang salah ketik (misal `#stok-a` ditulis `#stok_a`), sistem JavaScript akan langsung mati terhenti dan data kartu tersebut tidak akan pernah ter-update selamanya.

### Jawaban 3: Konsep Data Binding
*   **Data Binding** adalah proses pengikatan tali memori antara variabel JavaScript dengan elemen visual HTML di layar browser.
*   Pemberian tali pengikat ini membuat browser memantau variabel tersebut secara ketat (*Reactive Listener*). Begitu terjadi perubahan nilai variabel di memori, tali pengikat akan otomatis menarik dan menggambar ulang piksel visual di layar koordinat yang bersangkutan tanpa perlu intervensi pencarian elemen manual dari developer.
