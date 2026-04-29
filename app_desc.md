# Dokumentasi Proyek: BPS Sulut Indikator Mobile

## 1. High-Level Overview
Aplikasi ini adalah dashboard _mobile-first_ untuk menyajikan Indikator Strategis Badan Pusat Statistik (BPS) Provinsi Sulawesi Utara. Aplikasi ini memecahkan masalah aksesibilitas data BPS dengan merangkum data kompleks ke dalam bentuk "poster" digital atau kartu-kartu metrik yang interaktif, mudah dibaca, dan cepat diakses melalui perangkat mobile. Aplikasi juga menyediakan kemampuan _offline support_ dan penyimpanan data indikator favorit pengguna.

## 2. Tech Stack
Aplikasi dibangun menggunakan ekosistem modern untuk pengembangan hybrid mobile:
- **React Native & Expo**: Framework utama untuk membangun aplikasi iOS dan Android menggunakan satu basis kode (JavaScript/React).
- **Expo Router**: Sistem navigasi berbasis folder/file (mirip Next.js) yang mempermudah _routing_ antar halaman.
- **TypeScript**: Memberikan keamanan penulisan kode (_type-safety_) untuk mengurangi bug dan mempermudah pemahaman struktur data.
- **Axios**: _Library_ untuk melakukan pemanggilan HTTP Request ke Web API BPS.
- **Expo SQLite**: Sistem database lokal relasional (SQLite) yang digunakan untuk _caching_ data API agar aplikasi tetap responsif dan bisa diakses saat koneksi lambat/terputus.
- **AsyncStorage**: Penyimpanan _key-value_ sederhana untuk menyimpan preferensi pengguna (seperti mode tema atau pengaturan ringan lainnya).
- **Day.js**: Digunakan untuk mengelola dan memformat data waktu dan tanggal rilis dengan cara yang ringan.

## 3. Folder Anatomy
Berikut adalah struktur folder utama agar Anda tahu letak setiap file:
- **`app/`** -> Pusat _routing_ navigasi. Di sinilah halaman-halaman (screens) aplikasi berada. 
- **`src/api/`** -> Pengelolaan pemanggilan layanan backend/API BPS, tipe data, serta tempat menyimpan data *mock* (contoh: `feb.json`) untuk _testing_ atau _fallback_.
- **`src/components/`** -> Kumpulan komponen antarmuka UI yang bisa dipakai ulang (seperti `IndicatorCard`, `Segmented`).
- **`src/db/`** -> Konfigurasi dan repositori logika query SQLite untuk _caching_ data dan bookmark fitur.
- **`src/hooks/`** -> Tempat berkumpulnya *Custom React Hooks* (contoh: `usePosterData.ts`) yang memisahkan logika pemrosesan data dari tampilan UI.
- **`src/storage/`** -> Modul pengelolaan data ringan berbasis _key-value_ menggunakan AsyncStorage.
- **`src/theme/`** -> Pusat pengaturan desain sistem seperti skema warna, spasi, dan tipografi dasar.
- **`src/utils/`** -> Berisi fungsi-fungsi *helper* kecil, seperti _formatter_ angka, tanggal, atau adapter data (transformasi data API ke bentuk UI).
- **`assets/`** -> Tempat penyimpanan berkas gambar statis seperti logo BPS.

## 4. Data Flow & Logic
Bagaimana data mengalir dalam aplikasi ini? Mari kita gunakan **Skenario Membuka Halaman Utama (Dashboard)**:

1. **Aplikasi Dimuat**: File `app/_layout.tsx` berjalan pertama kali. Di sini, sistem inisialisasi database SQLite lokal (`initDb()`) dijalankan.
2. **Halaman Home Tampil**: `app/index.tsx` di-_render_ ke layar dan memanggil *custom hook* `usePosterData()`.
3. **Pencarian Data (Cache-First)**: Hook akan terlebih dahulu memeriksa database SQLite lokal. Jika data indikator terakhir masih ada (di-cache), aplikasi akan langsung menampilkannya (sehingga _loading_ terasa sangat cepat).
4. **Sinkronisasi API di Belakang**: Secara asinkron, Axios (`src/api/`) akan memanggil API Web BPS untuk meminta data terbaru.
5. **Update State & Cache**: Ketika balasan API terbaru diterima, data akan difilter/diadaptasi melalui `src/utils/posterAdapter.ts`. UI akan langsung diperbarui dengan angka terbaru, dan data tersebut disimpan kembali (_cached_) ke database SQLite (`src/db/posterCacheRepo.ts`) untuk kunjungan berikutnya.

## 5. Key Entry Points
Jika Anda ingin memahami _core logic_ proyek ini, bukalah file-file ini secara berurutan:
1. `app/_layout.tsx` - File bungkus utama (Root Layout) dan tempat Database dikonfigurasi.
2. `app/index.tsx` - Halaman paling depan dari aplikasi. Lihat bagaimana UI dirender dan data digabungkan.
3. `src/hooks/usePosterData.ts` - "Otak" utama dari _fetching_ dan _state management_ halaman depan.
4. `src/db/sqlite.ts` - Memahami skema dan persiapan awal _caching_ database lokal.

## 6. Environment & Setup
Langkah-langkah untuk menjalankan aplikasi secara lokal:
1. Buka terminal di root proyek.
2. Jalankan perintah instalasi _package_:
   ```bash
   npm install
   # atau jika menggunakan yarn: yarn install
   ```
3. Mulai server *development* Expo:
   ```bash
   npx expo start
   ```
4. Anda akan melihat **QR Code** di terminal. 
   - Untuk menguji di HP (Android/iOS): Unduh aplikasi **Expo Go**, lalu _scan_ QR tersebut.
   - Untuk membuka di Emulator Android: Tekan tombol `a`.

## 7. Pola Desain (Design Patterns)
- **Repository Pattern**: Aplikasi memisahkan kode query SQL secara spesifik ke dalam "repositori" (`src/db/savedIndicatorsRepo.ts` dan `src/db/posterCacheRepo.ts`). UI tidak pernah menulis sintaks SQL secara langsung.
- **Custom Hooks**: Logika pengambilan data API yang kompleks dipisahkan dari komponen tampilan (View). Komponen UI (`app/index.tsx`) hanya memanggil hook `usePosterData` dan fokus untuk me-render apa yang dikembalikan.
- **File-Based Routing**: Dengan menggunakan *Expo Router*, struktur file secara harfiah menggambarkan navigasi (Misal: `app/index.tsx` untuk path awal `/` dan `app/indicator/[var].tsx` untuk path detil indikator `/indicator/:var`).

## 8. Panduan Kontribusi untuk Junior
Jika Anda baru bergabung, ini 3 hal pertama yang sangat direkomendasikan untuk Anda kerjakan guna melatih pemahaman:
1. **Pemanasan UI**: Coba buka file `src/theme/colors.ts` dan ubah beberapa _Hex color_ dasar (misal background), lalu lihat perubahannya di aplikasi.
2. **Pahami Komponen Reusable**: Buka file `src/components/indicatorCard.tsx`. Cobalah untuk mengubah ukuran teks atau tambahkan satu baris statis baru (seperti "Sumber: BPS") di dalam kartu indikator tersebut.
3. **Pelajari Alur Bookmark**: Jalankan aplikasi, cobalah _save_ (bookmark) sebuah indikator di halaman depan. Lalu telusuri bagaimana tombol "Save" di kartu mengalir ke file `app/index.tsx`, hingga akhirnya menyimpan ID di `src/db/savedIndicatorsRepo.ts`.
