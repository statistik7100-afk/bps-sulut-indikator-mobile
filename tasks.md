# Action Plan: Audit Proyek BPS Sulut Indikator Mobile

Dokumen ini berisi hasil analisis mendalam (deep analysis) dan rencana aksi berdasarkan audit kode pada repository saat ini. Fokus audit meliputi aliran data API, UI/UX, dan _tech debt_.

## 1. Resolusi Data API vs Baseline (Logic & Data Flow)

### Task ID: TASK-API-01 | Dinamisasi Metadata Rilis Indikator
- **Kategori:** Data/API
- **Kondisi Saat Ini (Issue):** Di dalam `src/hooks/usePosterData.ts`, parameter `meta` (bulan, tahun rilis) masih di-*hardcode* (`release_month: "Februari", release_year: 2026`). Terdapat fungsi `extractLatestMeta` di file yang sama namun tidak digunakan (_dead code_).
- **Instruksi Eksekusi:** Hapus nilai _hardcode_ pada fungsi `fetchPosterPayload`. Panggil `extractLatestMeta` dari respons API `raw` terlebih dahulu, kemudian berikan hasil ekstraksi tersebut ke payload _meta_.
- **Estimasi Effort:** Low

### Task ID: TASK-API-02 | Modernisasi Mapping Baseline (Data Seeding vs Web Admin)
- **Kategori:** Data/API
- **Kondisi Saat Ini (Issue):** `BASELINE_INDICATORS_V2` di `src/api/indicatorPoster.ts` menggunakan _hardcode array_. Ketika data API BPS berubah (`indicator_id`, `var`, atau struktur judul), beberapa kartu akan gagal _match_ atau bernilai _null_.
- **Instruksi Eksekusi:** Mengembangkan Web Admin untuk mapping ini memakan waktu (High Effort). **Rekomendasi paling hemat waktu (Low Effort, High Impact)**: Pisahkan `BASELINE_INDICATORS_V2` menjadi file JSON (`baseline.json`) dan letakkan di _Remote Config_ (misal GitHub Gist raw URL, AWS S3, atau Firebase Remote Config). Aplikasi memuat mapping ini saat _startup_ (OTA Updates). Sebagai alternatif, buat script _Data Seeding_ (Node.js) di luar aplikasi mobile yang menarik data API BPS terbaru, mencocokkan _keywords_, dan meng-generate file JSON konfigurasi.
- **Estimasi Effort:** Medium

---

## 2. Audit UI/UX & Responsiveness (Front-end)

### Task ID: TASK-UI-01 | Perbaikan Interaksi Kartu (Pressable Bug)
- **Kategori:** UI/UX
- **Kondisi Saat Ini (Issue):** Pada komponen `src/components/indicatorCard.tsx`, properti `onPress` (untuk menavigasi ke halaman detail) diabaikan. Komponen pembungkus `Pressable` justru diikat ke `onToggleSave`. Akibatnya, saat pengguna mengetuk kartu, kartu tersebut malah di-*bookmark* dan tidak membuka halaman detail.
- **Instruksi Eksekusi:** Pindahkan pemanggilan `onToggleSave` ke sebuah ikon/tombol _bookmark_ khusus (misalnya ikon pita/bintang di pojok kartu). Gunakan prop `onPress` pada pembungkus utama `Pressable` kartu.
- **Estimasi Effort:** Low

### Task ID: TASK-UI-02 | Responsivitas Grid Tablet & Layar Lebar
- **Kategori:** UI/UX
- **Kondisi Saat Ini (Issue):** Pada `app/index.tsx`, deretan kartu diproses menggunakan `chunk(list, 3)` dan di-*render* dengan `flex: 1`. Di perangkat Tablet atau layar _landscape_, kartu akan memanjang secara horizontal dan merusak estetik.
- **Instruksi Eksekusi:** Ganti pembagian baris statis (`chunk 3`) dengan penghitungan kolom dinamis berdasarkan lebar layar (`Dimensions.get("window").width`). Alternatif termudah: berikan nilai `maxWidth` pada setiap kartu dan gunakan `flexWrap: "wrap"` pada kontainer.
- **Estimasi Effort:** Medium

### Task ID: TASK-UI-03 | Implementasi Aksesibilitas (A11y)
- **Kategori:** UI/UX
- **Kondisi Saat Ini (Issue):** Komponen interaktif seperti tombol Segmented dan IndicatorCard tidak memiliki properti *Accessibility* untuk pengguna Screen Reader.
- **Instruksi Eksekusi:** Tambahkan `accessibilityRole="button"` dan `accessibilityLabel` yang deskriptif pada elemen `Pressable` (termasuk label dinamis untuk indikator, misal: "Buka detail indikator Inflasi, nilai 5 persen").
- **Estimasi Effort:** Low

---

## 3. Discovery Tugas yang Tersembunyi (Blind Spots & Tech Debt)

### Task ID: TASK-TECH-01 | Penanganan Error (Error Handling) API
- **Kategori:** Tech Debt
- **Kondisi Saat Ini (Issue):** Saat proses *fetching* (`load` atau `refresh`) di `usePosterData.ts` mengalami kegagalan (misalnya internet mati atau API *down*), error disembunyikan dan sistem hanya menonaktifkan *loading state* melalui blok `finally`. Pengguna tidak mendapatkan _feedback_.
- **Instruksi Eksekusi:** Tangkap (*catch*) error pada fungsi `load` dan `refresh`. Simpan pesan *error* di dalam *state*. Tampilkan *Toast Notification* atau layar *Fallback/Empty State* yang ramah jika data gagal diambil.
- **Estimasi Effort:** Low

### Task ID: TASK-TECH-02 | Typings Ref SectionList
- **Kategori:** Tech Debt
- **Kondisi Saat Ini (Issue):** Pada `app/index.tsx`, pemanggilan fungsi *scroll-to-top* mem-Bypass _Type Checking_ dengan konversi `any` (`(listRef.current as any)?.getScrollResponder()`).
- **Instruksi Eksekusi:** Definisikan tipe ref dengan benar menggunakan `React.RefObject<SectionList<IndicatorRow>>` dan panggil `listRef.current?.scrollToLocation({ itemIndex: 0, sectionIndex: 0 })`.
- **Estimasi Effort:** Low
