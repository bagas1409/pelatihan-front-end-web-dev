# Hari 31: Auto-Imports & Pengelompokan Komponen
## Mengorganisasi Folder Components dengan Sub-direktori Bersarang Tanpa Memecah Fitur Auto-Import

Selamat datang di Hari 31! Hari ini kita akan mempelajari cara mengelola folder komponen di Nuxt 3 agar rapi dan terorganisasi dengan baik saat proyek frontend kita mulai memiliki puluhan berkas komponen.

Secara default, Nuxt 3 mendeteksi file komponen di dalam folder `components/`. Namun, jika kita menaruh semua file secara acak di dalam 1 level folder tersebut (seperti file button, modal, form, tabel semuanya bertumpuk), folder akan menjadi sangat semrawut. Kita akan belajar cara membuat sub-folder khusus (seperti `components/ui/`, `components/dashboard/`) dan memahami bagaimana Nuxt membaca nama filenya secara cerdas.

---

## 1. Aturan Penamaan Sub-folder di Nuxt 3

Ketika Anda membuat sub-folder di dalam direktori `components/`, Nuxt 3 secara otomatis menyusun nama komponen panggilannya dengan **menggabungkan nama folder dengan nama berkasnya** (*PascalCase*).

### Contoh Struktur Folder & Nama Panggilannya:

| Struktur Lokasi File Fisik | Nama Tag HTML Panggilannya di Berkas Lain |
| :--- | :--- |
| `components/ButtonDinas.vue` | `<ButtonDinas />` |
| `components/ui/ButtonDinas.vue` | `<UiButtonDinas />` |
| `components/dashboard/stok/TabelPersediaan.vue` | `<DashboardStokTabelPersediaan />` |
| `components/dashboard/modal/ConfirmCito.vue` | `<DashboardModalConfirmCito />` |

*Keunggulan*: Sistem penggabungan nama ini sangat hebat karena mencegah terjadinya tabrakan nama komponen (*Name Collision*) jika kita memiliki dua file dengan nama `Button.vue` yang berbeda fungsi di folder lain.

---

## 2. Studi Kasus PMI: Membuat Komponen UI Button & Dashboard Card

Mari kita rancang struktur komponen yang bersih untuk UDD PMI Lampung.

1.  Buat folder baru bernama **`components/ui/`** (khusus komponen antarmuka primitif dasar).
2.  Buat folder baru bernama **`components/dashboard/`** (khusus panel bisnis dashboard).

### Berkas 1: Tombol Kustom (`components/ui/Button.vue`)
```html
<!-- components/ui/Button.vue -->
<template>
  <button 
    class="px-4 py-2 text-xs font-bold rounded-xl transition-all active:scale-[0.98]"
    :class="variant === 'bahaya' ? 'bg-red-500 hover:bg-red-600 text-white' : 'bg-slate-100 hover:bg-slate-200 text-slate-700'"
  >
    <!-- Slot untuk menerima teks kustom dari luar -->
    <slot />
  </button>
</template>

<script setup>
defineProps({
  variant: {
    type: String,
    default: 'normal'
  }
});
</script>
```

### Berkas 2: Kartu Status (`components/dashboard/Card.vue`)
```html
<!-- components/dashboard/Card.vue -->
<template>
  <div class="p-6 bg-white rounded-3xl border border-slate-200 shadow-sm space-y-4">
    <h4 class="text-xs font-bold text-slate-400 uppercase tracking-wider">{{ judul }}</h4>
    <p class="text-3xl font-extrabold text-slate-800">{{ nilai }}</p>
    
    <!-- Memanggil komponen UiButton secara instan tanpa baris import -->
    <UiButton variant="normal">Detail Analisis</UiButton>
  </div>
</template>

<script setup>
defineProps({
  judul: String,
  nilai: [Number, String]
});
</script>
```

### Berkas 3: Halaman Utama (`pages/index.vue`)
Kita panggil komponen kartu dashboard dengan tag `<DashboardCard />`:

```html
<!-- pages/index.vue -->
<template>
  <div class="p-8 max-w-sm mx-auto space-y-4">
    <h2 class="text-lg font-bold">Ringkasan Pagi</h2>
    
    <!-- Memanggil komponen bersarang -->
    <DashboardCard judul="Antrean Donor" nilai="12 Orang" />
  </div>
</template>
```

---

## 3. Latihan Soal Mandiri
1. Amati tabel penggabungan nama berkas komponen di atas.
2. Jika Anda membuat file baru di lokasi `components/form/input/EmailValidator.vue`, tuliskan bagaimana nama tag HTML untuk memanggil komponen tersebut di halaman index.
3. Sebutkan kelebihan penggabungan nama otomatis (*Prefix-based naming*) ini dalam menjaga kerapian kode proyek kolaborasi tim besar.

---

## 4. Kunci Jawaban Soal & Penjelasan

### Jawaban 1 & 2: Pemanggilan Komponen Bersarang
*   Nama tag panggilannya adalah: **`<FormInputEmailValidator />`**
*   **Alasan**: Nuxt menggabungkan nama folder bertingkat `form` dan `input` di depan nama berkas `EmailValidator` menggunakan format huruf besar di tiap awal suku kata (PascalCase).

### Jawaban 3: Keunggulan Prefix Naming
*   **Mencegah Tabrakan Nama**: Jika tim A membuat tombol untuk navigasi di folder `components/navigation/Button.vue`, dan tim B membuat tombol untuk formulir di folder `components/form/Button.vue`, sistem biasa akan error karena nama komponen bentrok. Di Nuxt, keduanya dibedakan menjadi `<NavigationButton />` dan `<FormButton />` tanpa konfigurasi manual.
*   **Kejelasan Konteks**: Saat membaca baris kode HTML halaman utama, developer lain langsung tahu dari folder mana komponen tersebut berasal hanya dengan membaca awalan nama tagnya.
