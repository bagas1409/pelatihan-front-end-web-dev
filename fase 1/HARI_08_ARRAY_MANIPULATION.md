# Hari 08: Manipulasi Array Modern
## Menguasai Map, Filter, Find, & Reduce untuk Memilah Data Golongan Darah & Logistik UDD

Selamat datang di Hari 08! Hari ini kita akan mempelajari cara mengolah kumpulan data (array) secara modern menggunakan metode-metode bawaan JavaScript: **`map()`**, **`filter()`**, **`find()`**, dan **`reduce()`**.

Saat kita menghubungkan frontend dengan database server backend PMI, data yang dikirim biasanya berupa daftar panjang berisi puluhan data relawan atau stok darah. Kita harus memilah data tersebut (misal: mencari relawan golongan darah O saja, atau menghitung total kantong terkumpul). Melakukan hal ini menggunakan perulangan kuno `for` sangat panjang dan tidak efisien. Kita akan mempelajari teknik pemrosesan data kilat.

---

## 1. Empat Metode Utama Pengolah Array

### 💡 Analogi Dunia Nyata: "Petugas Sortir Logistik UDD PMI"
Bayangkan Anda adalah komandan logistik gudang darah PMI yang berdiri di depan tumpukan 100 kantong darah masuk:
*   **`map()` (Mesin Tempel Label Baru)**: Anda mendatangi setiap kantong satu per satu, menempelkan barcode stiker baru di atasnya, lalu menaruhnya kembali ke antrean tanpa membuang satu kantong pun. (Merubah bentuk/format tiap data di array menjadi struktur baru).
*   **`filter()` (Jaring Saringan Golongan O)**: Anda memasang saring khusus yang hanya melewatkan kantong darah golongan O saja. Kantong bergolongan A dan B tertahan dibuang ke keranjang terpisah. (Menyaring data berdasarkan kriteria logika).
*   **`find()` (Mencari Satu Relawan Spesifik)**: Anda berteriak menggunakan pengeras suara: *"Siapa relawan yang memiliki nomor kartu 88?"*. Begitu ada 1 orang tunjuk jari, Anda mengambil datanya dan **langsung berhenti mencari**. (Mendapatkan 1 item data pertama yang cocok).
*   **`reduce()` (Kalkulator Penjumlah Total Berat)**: Anda menaruh satu per satu kantong di atas timbangan digital timbang akumulasi berat sehingga di akhir Anda mendapatkan satu angka total berat kumulatif. (Menggabungkan/mereduksi seluruh isi array menjadi satu nilai tunggal).

---

## 2. Struktur Penggunaan Kode Array Modern

### A. `.map()` (Transformasi Data)
```javascript
const daftarStok = [10, 20, 5];
// Kalikan dua semua stok untuk simulasi target peningkatan
const targetStok = daftarStok.map(stok => stok * 2); 
console.log(targetStok); // Output: [20, 40, 10]
```

### B. `.filter()` (Penyaringan Data)
```javascript
const relawan = [
  { nama: "Budi", golDarah: "O" },
  { nama: "Siti", golDarah: "A" },
  { nama: "Joko", golDarah: "O" }
];
// Saring yang hanya bergolongan darah O saja
const golO = relawan.filter(orang => orang.golDarah === "O");
console.log(golO); // Output: [{ nama: "Budi", golDarah: "O" }, { nama: "Joko", golDarah: "O" }]
```

---

## 3. Studi Kasus PMI: Mengolah Dashboard Logistik Darah

Mari kita gabungkan metode-metode ini untuk mengolah data kompleks dari database backend PMI Lampung.

```javascript
const databaseStokPMI = [
    { id: 101, golongan: "A", jumlah: 15, region: "Bandar Lampung" },
    { id: 102, golongan: "B", jumlah: 8, region: "Metro" },
    { id: 103, golongan: "O", jumlah: 45, region: "Bandar Lampung" },
    { id: 104, golongan: "AB", jumlah: 3, region: "Lamsel" },
    { id: 105, golongan: "O", jumlah: 20, region: "Metro" }
];

// 1. Ambil data stok kritis di bawah 10 kantong (FILTER)
const stokKritis = databaseStokPMI.filter(item => item.jumlah < 10);
console.log("Stok Kritis:", stokKritis);

// 2. Cari rincian data khusus golongan AB (FIND)
const detailAB = databaseStokPMI.find(item => item.golongan === "AB");
console.log("Detail Golongan AB:", detailAB);

// 3. Hitung total seluruh kantong darah golongan O di Lampung (FILTER + REDUCE)
const totalGolO = databaseStokPMI
    .filter(item => item.golongan === "O")
    .reduce((akumulator, item) => akumulator + item.jumlah, 0); // Angka 0 adalah nilai awal hitungan

console.log(`Total Kantong Golongan O Tersedia: ${totalGolO} Kantong`);
```

---

## 4. Latihan Soal Mandiri
1. Amati kode manipulasi data di atas.
2. Tuliskan baris kode JavaScript menggunakan `.map()` untuk merubah array objek `databaseStokPMI` menjadi array string sederhana yang hanya berisi nama kota region saja (Output yang diharapkan: `["Bandar Lampung", "Metro", "Bandar Lampung", "Lamsel", "Metro"]`).
3. Jelaskan apa perbedaan mendasar hasil output antara metode `.filter()` dan `.find()` saat mencari data yang tidak terdaftar di dalam array.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Mapping Region Array
Untuk menyarikan nama region saja dari array objek, kita menggunakan `.map()`:

```javascript
const daftarKotaRegion = databaseStokPMI.map(item => item.region);
console.log(daftarKotaRegion); 
// Output: ["Bandar Lampung", "Metro", "Bandar Lampung", "Lamsel", "Metro"]
```

### Jawaban 3: Perbedaan Filter vs Find
*   **`.filter()` (Pencari Kolektif)**: Selalu menghasilkan output berupa **Array Baru** (meskipun kosong `[]` jika data tidak ditemukan, atau berisi banyak item jika ada beberapa kecocokan).
*   **`.find()` (Pencari Tunggal)**: Selalu mengembalikan **Satu Objek Tunggal** (atau nilai `undefined` jika data yang dicari tidak ditemukan sama sekali di dalam array).
*   *Penggunaan Taktis*: Gunakan `find()` jika Anda yakin hanya mencari satu data unik (seperti ID Relawan). Gunakan `filter()` jika Anda ingin mengelompokkan kategori (seperti daftar relawan yang belum donor).
