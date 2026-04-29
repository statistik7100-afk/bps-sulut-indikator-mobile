# BPS Sulut Indikator Mobile 📊

Aplikasi dashboard *mobile-first* yang ringan dan interaktif untuk memantau Indikator Strategis Badan Pusat Statistik (BPS) Provinsi Sulawesi Utara. Dibangun dengan React Native dan Expo, aplikasi ini menyajikan data statistik yang kompleks menjadi "poster" digital yang mudah dipahami, didukung dengan fitur *offline caching* agar tetap dapat diakses dalam kondisi minim sinyal.

---

## ✨ Fitur Utama

- **Visualisasi Poster Digital:** Data statistik disajikan dalam format kartu/poster yang elegan dan mudah dibaca.
- **Dukungan Offline (Cache-First):** Memanfaatkan SQLite untuk menyimpan data terakhir yang diunduh, sehingga aplikasi memuat lebih cepat dan tetap berfungsi tanpa koneksi internet.
- **Bookmark Indikator:** Pengguna dapat menyimpan (bookmark) indikator favorit untuk akses cepat.
- **Performa Tinggi:** Dioptimalkan dengan arsitektur yang efisien dan pemisahan logika menggunakan Custom Hooks.
- **Cross-Platform:** Dapat berjalan di iOS dan Android dengan *codebase* tunggal.

## 🛠️ Tech Stack

Aplikasi ini dibangun menggunakan teknologi modern:

- **Framework Utama:** React Native & Expo
- **Routing:** Expo Router
- **Bahasa Pemrograman:** TypeScript
- **HTTP Client:** Axios
- **Penyimpanan Lokal:** Expo SQLite (untuk *caching* data) & AsyncStorage (untuk preferensi pengguna)
- **Utilitas Waktu:** Day.js

## 🚀 Instalasi & Menjalankan Aplikasi

Ikuti langkah-langkah di bawah ini untuk menjalankan aplikasi secara lokal di mesin Anda.

### Prasyarat

Pastikan Anda telah menginstal:
- [Node.js](https://nodejs.org/) (versi terbaru yang direkomendasikan LTS)
- Aplikasi **Expo Go** di *smartphone* Anda (tersedia di App Store / Google Play), atau siapkan Android Emulator/iOS Simulator.

### Langkah-langkah

1. **Clone repositori ini**
   ```bash
   git clone https://github.com/statistik7100-afk/bps-sulut-indikator-mobile.git
   cd bps-sulut-indikator-mobile
   ```

2. **Instal dependensi**
   ```bash
   npm install
   # atau jika Anda menggunakan yarn:
   # yarn install
   ```

3. **Jalankan server development**
   ```bash
   npx expo start
   ```

4. **Buka Aplikasi**
   - **Di Smartphone:** *Scan* QR code yang muncul di terminal menggunakan aplikasi Expo Go.
   - **Di Emulator Android:** Tekan tombol `a` di terminal.
   - **Di iOS Simulator (khusus macOS):** Tekan tombol `i` di terminal.

## 📁 Struktur Direktori Utama

Untuk panduan yang lebih komprehensif mengenai arsitektur kode, silakan baca [app_desc.md](app_desc.md).

```text
bps-sulut-indikator-mobile/
├── app/             # Routing halaman (menggunakan Expo Router)
├── assets/          # Gambar statis, logo, dan resource lainnya
└── src/             # Kode sumber utama
    ├── api/         # Konfigurasi Axios dan pengambil data dari backend
    ├── components/  # Komponen UI yang dapat digunakan kembali
    ├── db/          # Repositori SQLite untuk offline caching & bookmark
    ├── hooks/       # Custom React Hooks (mis. usePosterData)
    ├── storage/     # Pengaturan AsyncStorage
    ├── theme/       # Skema warna, font, dan variabel desain sistem
    └── utils/       # Fungsi pembantu (format angka, adapter data, dll)
```

## 🤝 Berkontribusi

Kami menyambut baik kontribusi! Bagi *junior developer* yang baru bergabung, silakan baca bagian **Panduan Kontribusi** di [app_desc.md](app_desc.md) untuk mendapatkan ide tugas-tugas awal (*good first issues*).

1. Lakukan *Fork* pada repositori ini.
2. Buat *branch* fitur Anda (`git checkout -b fitur/NamaFitur`).
3. Lakukan *commit* perubahan Anda (`git commit -m 'Menambahkan fitur XYZ'`).
4. *Push* ke branch tersebut (`git push origin fitur/NamaFitur`).
5. Buat *Pull Request*.

## 📄 Lisensi

[MIT License](LICENSE) (Jika Anda ingin menambahkan file lisensi nantinya).
