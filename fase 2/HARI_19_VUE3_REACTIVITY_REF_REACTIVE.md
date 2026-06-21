# Hari 19: Reaktivitas Vue 3 - Ref vs Reactive
## Memahami Cara Kerja ref() dan reactive() Serta Kapan Harus Memilih Jenis Pembungkus Data yang Tepat

Selamat datang di Hari 19! Hari ini kita akan membedakan dua pilar utama reaktivitas di Vue 3: **`ref()`** dan **`reactive()`**.

Keduanya memiliki tugas yang sama, yaitu membuat variabel JavaScript menjadi "sensitif/reaktif" agar setiap kali datanya berubah, tampilan visual di layar browser ikut berubah secara otomatis. Namun, di bawah kap mesinnya, cara kerja dan aturan penulisannya memiliki perbedaan taktis yang harus Anda kuasai agar terhindar dari bug data tidak ter-render.

---

## 1. Mengenal Ref vs Reactive

### 💡 Analogi Dunia Nyata: "Kemasan Vakum vs Lemari Brankas Kunci Kombinasi"
*   **`ref()` (Kemasan Vakum Plastik Tunggal)**: Digunakan untuk membungkus **satu item data tunggal** (seperti teks string, angka integer, atau nilai boolean).
    *   *Analogi*: Seperti membungkus satu buah apel menggunakan plastik kemasan vakum transparan. Untuk memakan apel tersebut, Anda harus merobek kemasan plastiknya terlebih dahulu (`namaVariabel.value`).
*   **`reactive()` (Lemari Brankas Banyak Laci)**: Digunakan khusus untuk membungkus **kumpulan objek tebal (Object/Array)**.
    *   *Analogi*: Seperti lemari brankas besar yang memiliki banyak laci bertingkat di dalamnya. Anda tidak perlu membongkar fisik brankasnya untuk mengambil barang. Anda cukup membuka laci nomor 1 atau laci nomor 2 secara langsung (`namaBrankas.laciSatu`). Properti di dalamnya diakses langsung tanpa properti `.value`.

---

## 2. Perbandingan Kode: Menulis Ref vs Reactive

Mari kita lihat perbedaan sintaks penulisan di dalam `<script setup>` Vue 3:

### Menggunakan `ref()` (Paling Sering Digunakan / Fleksibel):
Bisa menerima tipe data primitif (string, number, boolean) maupun objek.

```html
<script setup>
import { ref } from 'vue';

// 1. Tipe Primitif
const jumlahKantong = ref(15);
// 2. Tipe Objek
const relawan = ref({ nama: "Budi", gol: "O" });

const updateData = () => {
  jumlahKantong.value = 20;            // Wajib menggunakan ".value"
  relawan.value.nama = "Budi Santoso";  // Wajib menggunakan ".value"
};
</script>
```

### Menggunakan `reactive()` (Khusus Objek):
*Hanya bisa menerima objek*. Jika Anda memasukkan angka atau string polos ke dalam `reactive()`, Vue akan mengeluarkan peringatan error.

```html
<script setup>
import { reactive } from 'vue';

// Hanya menerima struktur objek/array tebal
const dataLogistik = reactive({
  jumlahKantong: 15,
  namaKurir: "Joko",
  region: "Lampung"
});

const updateData = () => {
  dataLogistik.jumlahKantong = 20; // Langsung diakses TANPA menggunakan ".value"
  dataLogistik.namaKurir = "Joko Susilo";
};
</script>
```

---

## 3. Kapan Harus Menggunakan Keduanya?

*   **Pilih `ref()` secara default**: Pilihan teraman untuk 90% kasus koding Anda. `ref()` sangat tangguh karena kita bisa menimpa satu objek utuh dengan objek baru hasil fetch API secara langsung: `relawan.value = dataBaruDariServer`.
*   **Pilih `reactive()` jika**: Anda memiliki formulir besar yang menampung banyak kolom input yang saling berhubungan (seperti formulir pendaftaran relawan baru) dan Anda ingin menghindari mengetik kata `.value` berulang-ulang di file JavaScript Anda.
    *   *Peringatan Keras*: Jika Anda menimpa objek `reactive` secara utuh: `dataLogistik = dataBaru`, **reaktivitas objek tersebut akan langsung pecah/mati selamanya** karena referensi memorinya terputus.

---

## 4. Latihan Soal Mandiri
1. Amati contoh kode penggunaan `ref()` dan `reactive()` di atas.
2. Jika Anda membuat variabel reaktif menggunakan `reactive()`: `const dataForm = reactive({ nama: "" })`, lalu di dalam fungsi Anda menulis perintah untuk mengosongkan form: `dataForm = { nama: "" }`. Jelaskan apa yang akan dialami oleh browser saat pengguna mengetik di form tersebut setelahnya.
3. Tuliskan bagaimana cara yang benar untuk memperbarui/mengosongkan isi objek `reactive()` di atas tanpa mematikan tali pengikat reaktivitasnya.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kehilangan Reaktivitas pada Objek Reactive
*   **Akibat**: Browser tidak akan menampilkan teks ketikan baru pengguna di layar web sama sekali (reaktivitas mati).
*   **Alasan**: Menulis `dataForm = { ... }` berarti kita menugaskan variabel tersebut untuk menunjuk ke alamat objek memori baru yang polos di RAM. Hal ini secara instan membuang/memutus objek Proxy reaktif asli yang dipasang oleh Vue 3 saat inisialisasi awal. Vue kehilangan sensor pelacak pada variabel baru tersebut.

### Jawaban 3: Cara Mengosongkan Objek Reactive Secara Benar:
Agar reaktivitas tetap hidup, kita tidak boleh menimpa wadah brankasnya. Kita harus membersihkan laci di dalamnya satu per satu:

```javascript
// Cara 1: Mengubah properti dalamnya secara spesifik
dataForm.nama = "";

// Cara 2: Jika propertinya sangat banyak, gunakan Object.assign
Object.assign(dataForm, { nama: "", email: "", nomorHp: "" });
```
Metode `Object.assign()` menyalin nilai baru ke dalam wadah Proxy lama yang sama, sehingga reaktivitas database visual tetap terikat aman.
