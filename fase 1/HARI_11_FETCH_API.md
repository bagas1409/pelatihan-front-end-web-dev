# Hari 11: Fetch API & Request Handling
## Menarik Data Stok Darah Riil Melalui HTTP Request (GET, POST, PATCH) ke Server Backend

Selamat datang di Hari 11! Hari ini kita akan mempelajari cara menggunakan fungsi bawaan browser **`fetch()`** untuk melakukan komunikasi data riil ke server API backend menggunakan berbagai macam metode HTTP (GET, POST, PATCH, DELETE).

Di Hari 10 kita menyimulasikan data tiruan di memori lokal. Hari ini kita akan mempraktikkan cara mengirim request sungguhan menembus jaringan internet untuk meminta data, membuat data baru, dan memperbarui data persediaan darah di server database PMI.

---

## 1. Memahami Empat Metode Utama HTTP Request

Saat berkomunikasi dengan server API, client harus menentukan niat aksinya lewat metode HTTP:
1.  **`GET`**: Membaca / mengambil data dari database (contoh: Membaca daftar stok darah). Tidak mengirim payload body data.
2.  **`POST`**: Membuat / mengirim data baru ke database (contoh: Pendaftaran akun relawan baru). Mengirim payload body dalam format JSON.
3.  **`PATCH` / `PUT`**: Memperbarui data yang sudah ada di database (contoh: Menambah jumlah persediaan kantong darah).
4.  **`DELETE`**: Menghapus data dari database (contoh: Menghapus agenda donor yang batal diselenggarakan).

---

## 2. Struktur Sintaks Fetch API Bawaan Browser

Secara default, fungsi `fetch()` menggunakan metode `GET`. Jika ingin mengirim data (`POST` / `PATCH`), kita wajib menyertakan objek konfigurasi di parameter kedua.

### Contoh Request POST (Mengirim Data):
```javascript
const kirimDataRelawan = async (dataBaru) => {
  const respon = await fetch("https://api.pmi.com/relawan", {
    method: "POST", // Metode HTTP
    headers: {
      "Content-Type": "application/json" // Mengabarkan server bahwa kita mengirim format JSON
    },
    body: JSON.stringify(dataBaru) // Mengubah objek JS menjadi teks string JSON mentah
  });
  
  const hasil = await respon.json();
  return hasil;
};
```

---

## 3. Studi Kasus PMI: Integrasi Tiga Operasi API di Dashboard

Berikut adalah implementasi fungsi asinkron lengkap untuk mengelola persediaan logistik darah di UDD Lampung.

```javascript
const BASE_URL = "https://pmi-backend-api.onrender.com/api";

// 1. GET METHOD: Ambil Daftar Seluruh Stok Darah
const dapatkanSemuaStok = async () => {
    try {
        const respon = await fetch(`${BASE_URL}/stocks`);
        
        // Cek status HTTP respon (apakah 200, 404, dll)
        if (!respon.ok) {
            throw new Error(`Koneksi Gagal: Status HTTP ${respon.status}`);
        }
        
        const dataJson = await respon.ok ? await respon.json() : [];
        return dataJson;
    } catch (err) {
        console.error("Gagal mengambil stok:", err.message);
        return null;
    }
};

// 2. POST METHOD: Kirim Pendaftaran Donor Baru
const daftarkanDonorBaru = async (payload) => {
    try {
        const respon = await fetch(`${BASE_URL}/donations`, {
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify(payload)
        });

        if (!respon.ok) {
            const errorData = await respon.json();
            throw new Error(errorData.pesan || "Gagal mendaftarkan donor.");
        }

        return await respon.json();
    } catch (err) {
        return { sukses: false, error: err.message };
    }
};

// 3. PATCH METHOD: Update Kuantitas Stok setelah Transfusi (Mengurangi/Menambah)
const perbaruiJumlahStok = async (idStok, jumlahBaru) => {
    try {
        const respon = await fetch(`${BASE_URL}/stocks/${idStok}`, {
            method: "PATCH",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({ jumlah: jumlahBaru })
        });

        return await respon.json();
    } catch (err) {
        return { sukses: false, error: err.message };
    }
};
```

---

## 4. Latihan Soal Mandiri
1. Amati kode fungsi `daftarkanDonorBaru` di atas.
2. Mengapa kita wajib menuliskan baris `JSON.stringify(payload)` di dalam opsi `body` fetch saat melakukan request POST/PATCH? Apa yang akan terjadi jika kita langsung menuliskan `body: payload` tanpa diubah menjadi string?
3. Jelaskan apa perbedaan fungsi properti respon `respon.status` dengan `respon.ok` pada penanganan status error koneksi internet.

---

## 5. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pentingnya JSON Stringify
*   Protokol internet HTTP hanya bisa mengirimkan data dalam bentuk aliran **teks string polos** (*Text Stream*). HTTP tidak memahami tipe data memori internal JavaScript (seperti objek `{}` atau array `[]`).
*   Jika kita langsung menuliskan `body: payload` (mengirim objek mentah), browser secara otomatis akan mengonversi objek tersebut menjadi string default bawaan JavaScript yaitu teks **`"[object Object]"`**. Server API di backend akan mengalami error fatal saat parsing teks karena teks tersebut tidak memiliki format data JSON terstruktur yang valid. Fungsi `JSON.stringify()` bertugas menerjemahkan objek memori JS menjadi teks format JSON standar industri (`{"nama": "Budi"}`).

### Jawaban 3: Perbedaan status vs ok
*   **`respon.status`**: Mengembalikan nilai **Angka Integer** spesifik kode status HTTP dari server (contoh: `200`, `201`, `400`, `404`, `500`).
*   **`respon.ok`**: Mengembalikan nilai **Boolean** (`true`/`false`). Nilai akan bernilai `true` secara instan jika kode status HTTP berada di kisaran sukses **200 s.d 299**, dan otomatis bernilai `false` jika kode status berada di luar rentang tersebut (seperti error client 4xx atau error server 5xx). Ini memudahkan kita membuat percabangan validasi singkat.
