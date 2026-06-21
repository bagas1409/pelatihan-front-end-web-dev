# Hari 09: Destructuring & Spread Operator
## Menyederhanakan Pembongkaran Data Profil Relawan & Penggabungan List Logistik

Selamat datang di Hari 09! Hari ini kita akan mengulas dua fitur ES6 terfavorit bagi developer Vue/Nuxt: **Destructuring** (Pembongkaran Struktur) dan **Spread/Rest Operator** (Operator Titik Tiga `...`).

Ketika menerima objek data tebal dari database backend (seperti data biodata lengkap relawan), kita seringkali hanya membutuhkan nama dan nomor teleponnya saja untuk ditampilkan di layar dashboard. Menulis kode berulang seperti `const nama = data.relawan.biodata.nama` sangat panjang dan melelahkan. Kita akan belajar cara membongkarnya dalam 1 baris bersih.

---

## 1. Konsep Destructuring (Pembongkaran)

### 💡 Analogi Dunia Nyata: "Membongkar Koper Barang Bawaan"
Bayangkan Anda menerima paket koper logistik PMI Lampung berisi obat, kain kasa, dan gunting medis.
*   **Cara Tradisional**: Anda merogoh koper, mengambil obat, menaruhnya di meja. Merogoh lagi, mengambil kain kasa, menaruhnya di meja.
*   **Destructuring**: Anda menaruh koper di meja, lalu sekali buka kunci koper, semua barang langsung terlempar tertata di posisinya masing-masing secara instan.

### Kode Penerapan Destructuring Objek:
```javascript
const profilRelawan = {
  nama: "Budi Santoso",
  nik: "1871052208000001",
  alamat: "Jl. Kartini No. 5 Bandar Lampung",
  kontak: {
    telepon: "08123456789",
    email: "budi@gmail.com"
  }
};

// Cara Lama:
const namaLama = profilRelawan.nama;
const emailLama = profilRelawan.kontak.email;

// Cara Baru (Destructuring):
const { nama, alamat } = profilRelawan;
const { email } = profilRelawan.kontak; // Pembongkaran bertingkat

console.log(nama); // Output: Budi Santoso
console.log(email); // Output: budi@gmail.com
```

---

## 2. Spread Operator (`...` - Pengurai / Penggabung)
Operator titik tiga memiliki kemampuan mengurai seluruh isi koper (array/objek) keluar, atau menggabungkan beberapa koper menjadi satu koper baru tanpa merusak data aslinya.

```javascript
// A. Menggabungkan Array
const stokLampung = ["O", "A"];
const stokMetro = ["B", "AB"];

// Menggabungkan kedua array menjadi satu
const totalStok = [...stokLampung, ...stokMetro];
console.log(totalStok); // Output: ["O", "A", "B", "AB"]

// B. Menggabungkan & Meng-update Objek
const userLokal = { nama: "Budi", status: "Aktif" };
const userTerbaru = { ...userLokal, status: "Tidak Aktif", region: "Lampung" };
// Status ditimpa menjadi "Tidak Aktif" dan ditambahkan region baru
console.log(userTerbaru); // Output: { nama: "Budi", status: "Tidak Aktif", region: "Lampung" }
```

---

## 3. Studi Kasus PMI: Mengolah Payload Pendaftaran Donor

Di Vue/Nuxt, saat mengirim data pendaftaran relawan ke backend, kita seringkali ingin memisahkan data identitas utama dengan data kuesioner medis pelengkap menggunakan teknik **Rest Parameter**.

```javascript
const payloadPendaftaran = {
    nama: "Ani Lestari",
    usia: 22,
    golDarah: "A",
    beratBadan: 55,
    riwayatPenyakit: "Tidak Ada",
    tekananDarah: "120/80"
};

// Bongkar data utama, dan kelompokkan sisa data medis ke dalam variabel "kuesionerMedis"
const { nama, usia, golDarah, ...kuesionerMedis } = payloadPendaftaran;

console.log("Data Akun Relawan:");
console.log(`Nama: ${nama}, Usia: ${usia} Tahun, Golongan: ${golDarah}`);

console.log("Berkas Kuesioner Medis (Untuk Diserahkan ke Dokter):");
console.log(kuesionerMedis); 
// Output: { beratBadan: 55, riwayatPenyakit: "Tidak Ada", tekananDarah: "120/80" }
```

---

## 4. Latihan Soal Mandiri
1. Amati kode pemisahan data `payloadPendaftaran` di atas.
2. Tuliskan baris kode destructuring singkat untuk mengekstrak data `beratBadan` dan `tekananDarah` langsung dari variabel objek `kuesionerMedis` yang telah terpisah.
3. Mengapa kita disarankan menggunakan *Spread Operator* `...` untuk menggandakan (clone) suatu array/objek dibanding hanya menulis baris penugasan biasa seperti `const arrayBaru = arrayLama;`? Jelaskan hubungannya dengan tipe referensi memori (*Memory Reference Bug*).

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Destructuring dari kuesionerMedis
```javascript
const { beratBadan, tekananDarah } = kuesionerMedis;
console.log(`Berat: ${beratBadan} kg, Tensi: ${tekananDarah}`);
```

### Jawaban 3: Masalah Referensi Memori (Shallow Copy)
*   Di JavaScript, Array dan Objek adalah **Tipe Data Referensi**.
*   Jika Anda menulis `const arrayBaru = arrayLama;`, browser **tidak membuat salinan baru**. Kedua variabel tersebut menunjuk ke alamat memori fisik yang **sama** di dalam RAM komputer. 
*   Akibatnya: Jika Anda mengubah isi data di `arrayBaru`, secara mengejutkan isi data di `arrayLama` **akan ikut berubah rusak** karena keduanya berbagi alamat memori yang sama.
*   Dengan menggunakan Spread Operator (`const arrayBaru = [...arrayLama];`), kita memotong rantai referensi tersebut dan mencetak salinan data fisik baru yang terisolasi secara mandiri di RAM browser. Perubahan di array baru tidak akan mengganggu data asli.
