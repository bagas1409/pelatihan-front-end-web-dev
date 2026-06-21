# Hari 07: JavaScript ES6+ Modern
## Menguasai Let, Const, Arrow Functions, & Template Literals untuk Pengolahan Data Portal PMI

Selamat datang di Hari 07! Hari ini kita akan melangkah masuk ke dunia logika pemrograman frontend menggunakan **JavaScript ES6+ Modern**.

Sebelum kita menulis kerangka framework Vue/Nuxt, kita wajib fasih menggunakan gaya penulisan JavaScript modern. Cara lama menulis JS (menggunakan variabel `var` dan penggabungan teks memakai tanda tambah `+`) sudah ditinggalkan karena rawan memicu bug kebocoran memori. Kita akan mempelajari tiga pilar modern ES6 yang membuat penulisan kode kita rapi dan profesional.

---

## 1. Perbedaan Lingkup Variabel: Let vs Const vs Var

### 💡 Analogi Dunia Nyata: "Papan Tulis Sekolah vs Ukiran Batu Prasasti"
*   **`var` (Gaya Lama - Tulisan Pasir Pantai)**: Tulisannya mudah tertiup angin dan terhapus acak oleh ombak air laut. Variabel `var` tidak mengenal batas ruangan kurung kurawal `{}` (*No Block Scope*), sehingga sering tidak sengaja menimpa data penting lain di halaman web lain secara liar.
*   **`let` (Gaya Modern - Papan Tulis Kelas)**: Anda menulis angka stok darah di papan tulis menggunakan spidol. Anda bebas menghapus angka `10` dan menulis ulang dengan angka `12` jika ada donor baru masuk. Namun, tulisan ini hanya berlaku di dalam ruang kelas tersebut saja (*Block Scope*).
*   **`const` (Gaya Modern - Ukiran Batu Prasasti)**: Tanggal lahir atau nomor NIK KTP relawan diukir permanen di atas batu prasasti. Nilai ini **dilarang keras diubah** sepanjang program berjalan untuk menghindari kecurangan atau kesalahan input manual.

---

## 2. Sintaks Arrow Functions (Fungsi Panah)
Arrow functions memperingkas penulisan fungsi JavaScript tradisional dan mempertahankan referensi konteks kata kunci `this` secara otomatis.

```javascript
// Cara Lama (Tradisional):
function hitungKebutuhan(stok) {
  return 100 - stok;
}

// Cara Baru (Arrow Function):
const hitungKebutuhan = (stok) => 100 - stok;
```
*Keunggulan*: Jika fungsi hanya memiliki 1 baris instruksi pengembalian nilai (`return`), kita bisa menghapus kurung kurawal `{}` dan kata kunci `return` sehingga kode menjadi sangat ringkas dalam satu baris.

---

## 3. Template Literals (Penyisipan Variabel dalam Teks)
Menggabungkan string teks dengan variabel tidak perlu lagi menggunakan tanda tambah `+` yang merepotkan dan rawan typo spasi. Gunakan karakter backtick (`` ` ``) di keyboard Anda dan sisipkan variabel menggunakan simbol `${namaVariabel}`.

```javascript
const namaRelawan = "Budi Santoso";
const golDarah = "O";

// Cara Lama:
const pesanLama = "Relawan bernama " + namaRelawan + " memiliki golongan darah: " + golDarah;

// Cara Baru (Template Literals):
const pesanBaru = `Relawan bernama ${namaRelawan} memiliki golongan darah: ${golDarah}`;
```

---

## 4. Studi Kasus PMI: Mengolah Status Notifikasi CITO

Mari kita buat sebuah script fungsi sederhana untuk mencetak laporan kebutuhan darah darurat PMI Lampung menggunakan seluruh aturan ES6 modern.

```javascript
// 1. Deklarasikan data konstanta instansi (tidak boleh diubah)
const NAMA_UDD = "PMI Lampung UDD Kota";

// 2. Deklarasikan variabel let yang datanya dinamis
let stokDarahAB = 5;

// 3. Buat Arrow Function untuk memproses peringatan
const periksaUrgensiStok = (stok, minimalAman = 10) => {
    const statusCito = stok < minimalAman;
    
    // Kembalikan objek status lengkap
    return {
        isCito: statusCito,
        pesan: statusCito 
            ? `🚨 DARURAT CITO: Stok darah AB di ${NAMA_UDD} hanya tersisa ${stok} kantong!`
            : `✓ AMAN: Stok darah AB di ${NAMA_UDD} memadai (${stok} kantong).`
    };
};

// Panggil Fungsi
const hasilAnalisis = periksaUrgensiStok(stokDarahAB);
console.log(hasilAnalisis.pesan);
```

---

## 5. Latihan Soal Mandiri
1. Amati kode fungsi `periksaUrgensiStok` di atas.
2. Jika Anda mencoba menulis ulang baris kode di bawah fungsi tersebut dengan perintah: `NAMA_UDD = "PMI Lampung Selatan";`, sebutkan jenis pesan error apa yang akan dilempar oleh konsol JavaScript browser Anda dan jelaskan alasannya.
3. Ubahlah fungsi tradisional berikut menjadi bentuk *Arrow Function* satu baris paling ringkas:
   ```javascript
   function konversiKeMililiter(jumlahKantong) {
       return jumlahKantong * 350;
   }
   ```

---

## 6. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Aturan Const Assignment Error
*   Konsol browser akan melemparkan pesan error fatal: **`TypeError: Assignment to constant variable.`**
*   **Alasan**: Variabel yang dideklarasikan menggunakan kata kunci `const` dikunci rapat oleh mesin JavaScript sejak awal alokasi memori. Nilai tersebut bersifat read-only (hanya bisa dibaca) dan tidak diperbolehkan menerima penugasan nilai baru (*re-assignment*) sepanjang siklus jalannya aplikasi demi menjamin konsistensi data konfigurasi.

### Jawaban 3: Konversi ke Arrow Function Ringkas
```javascript
const konversiKeMililiter = jumlahKantong => jumlahKantong * 350;
```
*Mengapa sangat ringkas?*:
1. Karena parameter hanya berjumlah **1 buah** (`jumlahKantong`), kita boleh menghapus tanda kurung pembungkus parameter `()`.
2. Karena fungsi hanya bertugas mengembalikan perkalian baris tunggal, kita boleh membuang kurung kurawal `{}` dan kata kunci `return`.
