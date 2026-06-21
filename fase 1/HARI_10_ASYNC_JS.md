# Hari 10: Asynchronous JavaScript - Promise & Async/Await
## Memahami Cara Menunggu Respon API Server Backend Tanpa Mencekik Layar Browser Web

Selamat datang di Hari 10! Hari ini kita akan mempelajari konsep tersulit namun paling krusial dalam pemrograman frontend: **Asynchronous JavaScript** (Asinkron), **Promise**, dan fungsi **Async/Await**.

Saat website admin memanggil database API server backend PMI yang berada di internet (misal memuat 10.000 data transaksi), koneksi tersebut membutuhkan waktu jeda (misal: 1 detik). Di JavaScript, kita tidak boleh membiarkan layar website membeku (hang) selama proses tunggu tersebut. Kita harus membiarkan browser melakukan pekerjaan lain secara paralel. Mari kita pelajari caranya.

---

## 1. Perbedaan Synchronous vs Asynchronous

### 💡 Analogi Dunia Nyata: "Pemesanan Makanan di Kasir Bioskop vs Restoran Ramen"
*   **Synchronous (Antrean Kasir Bioskop - Lambat)**: Anda memesan tiket. Kasir pergi mencari tiket, mencetaknya, menyerahkannya. Selama 5 menit kasir bekerja, antrean di belakang Anda tertahan mematung tidak bisa dilayani sama sekali. Semua orang harus mengantre kaku (*Blocking Thread*).
*   **Asynchronous (Restoran Ramen - Cerdas/Paralel)**: Anda memesan mangkuk mie ramen di kasir. Kasir tidak menyuruh Anda berdiri mematung di depan meja kasir selama koki memasak mie. Kasir menyerahkan **alat buzzer alarm antrean** (disebut **Promise**). Anda bebas pergi duduk di pojok, memainkan HP, atau membaca koran. Begitu mie ramen matang di dapur, alarm buzzer bergetar berdering (Promise terpenuhi / *Resolved*), dan Anda berjalan mengambil makanan.

Di frontend, alarm buzzer tersebut adalah **Promise**, dan aktivitas menunggu alarm berdering secara santai adalah **Async/Await**.

---

## 2. Mengenal Objek Promise di JavaScript
Promise adalah janji penampung nilai di masa depan yang memiliki 3 status utama:
1.  **Pending**: Sedang dalam proses memasak/menunggu respon server.
2.  **Fulfilled (Resolved)**: Proses sukses, data berhasil diterima.
3.  **Rejected**: Proses gagal (koneksi mati/error 500), alarm berbunyi tanda error.

---

## 3. Gaya Modern: Menulis Async/Await
Kata kunci `async` dan `await` adalah cara tercantik menulis kode asinkron agar terlihat lurus dibaca seperti kode sinkron biasa tanpa jebakan rantai `.then()` yang berantakan (*Callback Hell*).

```javascript
// Fungsi wajib diawali kata kunci "async"
const ambilDataStok = async () => {
  try {
    // Jalankan pencarian server, dan kunci eksekusi baris bawah 
    // sampai server menjawab (await)
    const respon = await fetch("https://api.pmi.com/stocks");
    const data = await respon.json();
    console.log("Data Diterima:", data);
  } catch (error) {
    // Tangkap jika terjadi kegagalan koneksi/server mati
    console.error("Gagal memuat data:", error.message);
  }
};
```

---

## 4. Studi Kasus PMI: Simulasi Pengecekan Stok Darah Kritis

Mari kita buat kode simulasi pemanggilan API asinkron lengkap dengan penanganan delay waktu nyata menggunakan timer Promise.

```javascript
// 1. Simulasi Helper penunda waktu (menciptakan delay milidetik)
const delay = (milidetik) => new Promise(resolve => setTimeout(resolve, milidetik));

// 2. Fungsi Asinkron memanggil data server
const dapatkanStokDarahOnline = async (golongan) => {
    console.log(`[1] Memulai request koneksi ke server API UDD...`);
    
    try {
        // Simulasi menunggu delay internet selama 2 detik (2000ms)
        await delay(2000); 

        // Simulasi data tiruan dari database
        const databaseTiruan = {
            "A": 15,
            "B": 0,
            "AB": 2,
            "O": 99
        };

        const jumlahStok = databaseTiruan[golongan];

        if (jumlahStok === undefined) {
            throw new Error(`Golongan darah "${golongan}" tidak dikenal!`);
        }

        // Jika stok kosong, lempar warning CITO
        if (jumlahStok === 0) {
            return { sukses: true, jumlah: 0, status: "🚨 DARURAT CITO KOSONG!" };
        }

        return { sukses: true, jumlah: jumlahStok, status: "Aman" };

    } catch (err) {
        // Bungkus kegagalan agar tidak mematikan program utama
        return { sukses: false, pesanError: err.message };
    }
};

// 3. Menjalankan Fungsi Utama
const jalankanPengecekan = async () => {
    console.log("=== MULAI MONITOR STOK ===");
    
    // Panggil API (Proses asinkron berjalan di latar belakang)
    const hasil = await dapatkanStokDarahOnline("B");
    
    console.log("[2] Respon server diterima:");
    console.log(hasil);
    
    console.log("=== MONITOR SELESAI ===");
};

jalankanPengecekan();
```

---

## 5. Latihan Soal Mandiri
1. Amati jalannya eksekusi program pada fungsi `jalankanPengecekan` di atas.
2. Jika kita menghapus kata kunci **`await`** di depan fungsi panggilan `dapatkanStokDarahOnline("B")` (menjadi `const hasil = dapatkanStokDarahOnline("B");`), sebutkan apa tipe objek hasil yang akan tercetak di console log dan jelaskan mengapa browser tidak menampilkan rincian data stok darah aslinya.
3. Sebutkan fungsi blok pengaman **`try-catch`** di sekeliling fungsi await dan apa akibatnya bagi stabilitas web jika kita lupa memasangnya saat server backend tiba-tiba mati lampu.

---

## 6. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Kehilangan Await Properti
*   Objek yang tercetak di console log adalah **`Promise { <pending> }`**.
*   **Alasan**: Tanpa kata kunci `await`, mesin JavaScript tidak akan bersedia menghentikan sementara jalannya baris kode di bawahnya. JavaScript akan langsung melompat mengeksekusi `console.log(hasil)` saat itu juga. Karena proses delay 2 detik belum selesai berjalan di latar belakang, alarm buzzer (Promise) tersebut masih berstatus antre kosong (*Pending*), dan isi makanan (data stok) belum matang diproses.

### Jawaban 3: Kegunaan Try-Catch
*   **Fungsi `try-catch`**: Berfungsi sebagai jaring pengaman kegagalan asinkron.
*   **Akibat Jika Dihapus**: Jika server backend mati/padam dan kita tidak membungkus fungsi `await` dengan `try-catch`, browser akan mengalami error fatal yang tidak tertangani (*Unhandled Promise Rejection*). Pada banyak kasus, hal ini menyebabkan seluruh proses JavaScript di browser terhenti mendadak (macet), membuat pengguna tidak bisa mengklik tombol menu lain di website dan layar mematung putih.
