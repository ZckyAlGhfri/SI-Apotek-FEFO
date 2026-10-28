# DOKUMEN CETAK BIRU PERANCANGAN ANTARMUKA & INTERAKSI (UI/UX MASTER BLUEPRINT)
## Sistem Informasi Penjualan Apotek Berbasis Algoritma FEFO (First Expired, First Out)
**Sistem Aplikasi:** PharmaSentinel Enterprise POS & Inventory Engine  
**Versi Dokumen:** 1.0 (Final Design Freeze)  
**Institusi:** Institut Teknologi Al-Muhajirin Purwakarta (Prodi Sistem Informasi)  
**Penyusun:** Kelompok 6 (M. Zacky Alghifari, Abdulah Syauki, Komala Dewi, Nazla Khoerunisa, Denna Agustin)  
**Target Rilis Teknis:** C# (.NET 8), ASP.NET Core MVC, Entity Framework Core, MySQL  

---

## 1. PENDAHULUAN & FILOSOFI DESAIN

### 1.1 Latar Belakang Desain
Mayoritas sistem informasi manajemen apotek (Pharmacy POS) konvensional memiliki kelemahan mendasar pada antarmuka pengguna:
1. **Pola Desain Usang (*Mainstream Sidebar Clutter*):** Penggunaan bilah sisi kiri (*sidebar*) statis selebar 250px memakan area horizontal penting, membuat tabel batch obat dan struk kasir berdesak-desakan.
2. **Kelelahan Visual (*Ocular Fatigue*):** Dominasi latar putih polos tanpa kontras (*sterile hospital white*) menyulitkan pembedaan status batch kritis.
3. **Pencampuran Peran (*Role Confusion*):** Kasir sering kali disodori data sensitif (harga modal beli dari pabrik, margin laba), yang melanggar prinsip kerahasiaan bisnis farmasi dan memperlambat waktu transaksi.

### 1.2 Paradigma "Editorial Light Cockpit" & "Dynamic Island"
PharmaSentinel mengadopsi filosofi desain modern ala *software enterprise* kelas dunia (seperti Linear, Raycast, dan Apple macOS/iOS):
* **Dynamic Island Navigation:** Navbar kapsul mengambang (*floating pill*) di tengah atas yang bersifat adaptif. Saat diam (*idle*), navbar menyusut minimalis agar pandangan bebas gangguan; saat kursor mendekat (*hover*), navbar mekar (*morph spring animation*) menampilkan menu navigasi kontekstual.
* **Editorial Light Cockpit Canvas:** Kanvas dasar menggunakan abu-abu kebiruan sejuk (*Cool Slate* `#F1F5F9`) dengan kartu-kartu putih berbayang halus (`#FFFFFF`). Hal ini memberikan pemisahan elevasi yang tegas dan menghilangkan efek silau.
* **Separation of Duties (SoD):** Pembagian antarmuka tegas antara meja operasional depan (Kasir POS) dan meja manajerial logistik (Admin Gudang).

---

## 2. DESIGN TOKENS & PANDUAN GAYA VISUAL

### 2.1 Palet Warna & Rasio Kontras (WCAG 2.1 AAA Compliant)

| Token Desain | Kode Warna | Penggunaan Semantik | Rasio Kontras / Keterangan |
| :--- | :--- | :--- | :--- |
| `--bg-app` | `#F1F5F9` | Kanvas Latar Belakang Aplikasi | Slate-100; Sejuk, tidak menyilaukan mata |
| `--surface-card` | `#FFFFFF` | Permukaan Kartu Konten & Formulir | Pure White; Memberikan elevasi tegas di atas canvas |
| `--surface-glass-dark` | `rgba(15,23,42,0.95)`| Latar Dynamic Island Navbar | Slate-900 Glassmorphism + Backdrop Blur 24px |
| `--border-subtle` | `#E2E8F0` | Garis Batas Kartu & Pemisah Tabel | Slate-200; Halus namun tegas |
| `--border-strong` | `#CBD5E1` | Garis Batas Input & Field Formulir | Slate-300; Keterbacaan batas input tinggi |
| `--brand-teal` | `#0D9488` | Aksi Primer, Harga, Status Aktif | Teal-600; Identitas farmasi modern & higienis |
| `--brand-teal-light` | `#CCFBF1` | Latar Lencana (Badge) & Hover Aksen| Teal-100; Lembut untuk indikator visual |
| `--brand-teal-dark` | `#0F766E` | Hover State Tombol Primer | Teal-700; Menegaskan interaksi klik |
| `--danger-red` | `#E11D48` | Peringatan Kritis ED $\le 30$ Hari & Karantina | Rose-600; Menarik atensi seketika |
| `--danger-red-light`| `#FFE4E6` | Latar Badge Kedaluwarsa & Alert Kritis | Rose-100; Kontras tinggi dengan teks bahaya |
| `--warning-amber` | `#D97706` | Peringatan Waspada ED & Tag Split-Batch | Amber-600; Indikator kehati-hatian operasional |
| `--warning-amber-light`| `#FEF3C7`| Latar Badge Split-Batch & Stok Tipis | Amber-100; Lembut di mata |
| `--success-green` | `#059669` | Indikator Stok Aman & Status Committed | Emerald-600; Menandakan integritas data valid |
| `--text-heading` | `#0F172A` | Judul, Header Tabel, Angka Statis | Slate-900; Hitam pekat tajam anti-pudar |
| `--text-body` | `#1E293B` | Teks Konten Utama | Slate-800; Keterbacaan teks panjang optimal |
| `--text-muted` | `#475569` | Keterangan Sekunder, Label Sub-item | Slate-600; Tidak pudar, terbaca di layar proyektor |
| `--text-dim` | `#64748B` | Placeholder Formulir & Footnote | Slate-500; Pemandu masukan teks |

### 2.2 Tipografi
* **Font Family Utama:** `'Plus Jakarta Sans', system-ui, -apple-system, sans-serif`
* **Skala Tipografi:**
  * `Heading 1 (Page Title):` 1.75rem (28px) / SemiBold 700 / Tracking -0.5px
  * `Heading 2 (Card Title):` 1.15rem (18.4px) / SemiBold 700 / Tracking -0.2px
  * `Body Text Regular:` 0.88rem (14px) / Regular 400 / Line-Height 1.5
  * `Small Text / Metadata:` 0.76rem (12px) / Medium 500
  * `Micro Badge / Code Tag:` 0.68rem - 0.72rem (11px) / Bold 800 / Uppercase Monospace

### 2.3 Sistem Elevasi, Radius & Animasi
* **Border Radius:**
  * `--radius-pill`: `9999px` (Digunakan pada Dynamic Island, Tombol Aksi, Chip Filter, Field Input).
  * `--radius-card`: `20px` (Digunakan pada Container Utama, Bento Panel, dan Card Cockpit).
  * `--radius-item`: `16px` (Digunakan pada Kartu Produk Obat dan Kotak Status).
* **Bayangan (Shadows):**
  * `Default Elevation:` `0 4px 16px -2px rgba(15, 23, 42, 0.06), 0 2px 4px -1px rgba(15, 23, 42, 0.04)`
  * `Hover Elevation:` `0 12px 28px -4px rgba(13, 148, 136, 0.16)`
* **Kurva Pegas Animasi (Spring Physics):**
  * `Dynamic Island Transition:` `all 0.45s cubic-bezier(0.16, 1, 0.3, 1)`
  * `Card State Morphing:` `all 0.3s cubic-bezier(0.16, 1, 0.3, 1)`

---

## 3. ARSITEKTUR INFORMASI & MATRIKS HAK AKSES (RBAC)

### 3.1 Peta Halaman Sistem (Sitemap)

```text
[ ROOT GATEWAY ]
  └── index.html (Halaman Login & Pintasan Akses Cepat Demo)
       │
       ├──► [ DOMAIN KASIR - OPERASIONAL TRANSAKSI DEPAN ]
       │      ├── kasir-pos.html     (Katalog POS, Omni-Search HUD, Keranjang FEFO Adaptif)
       │      ├── kasir-riwayat.html (Log Transaksi Shift Kasir & Cetak Ulang Struk Thermal)
       │      └── kasir-stok.html    (Katalog Read-Only Ketersediaan & Lokasi Rak Obat)
       │
       └──► [ DOMAIN ADMIN GUDANG - LOGISTIK, AUDIT & KONTROL ]
              ├── admin-dashboard.html (Telemetri Eksekutif & Radar Antrean Kedaluwarsa H-90)
              ├── admin-batch.html     (Penerimaan Faktur Surat Jalan PBF & Anti-ED Lampau)
              ├── admin-karantina.html (Isolasi Fisik Obat ED, Surat Retur & Berita Acara Musnah)
              └── admin-katalog.html   (Master SKU Obat, Bentuk Sediaan & Setting Alert Stok)

3.2 Matriks Hak Akses & Isolasi Data (RBAC Matrix)

| Fitur / Modul Operasional            | Kasir POS              | Admin Gudang | Rationale & Alasan Desain Sistem                                         |
| :----------------------------------- | :--------------------: | :----------: | :----------------------------------------------------------------------- |
| **Login Multi-Peran**                | Ya                     | Ya           | Pintu masuk autentikasi tunggal.                                         |
| **Transaksi Penjualan (POS)**        | **Ya**                 | Tidak        | Menjaga integritas pemisahan tugas (*Separation of Duties*).             |
| **Melihat Harga Jual Konsumen**      | **Ya**                 | Ya           | Transparansi nominal belanja pasien.                                     |
| **Melihat Harga Modal Beli (PBF)**   | **DITUTUP**            | **Ya**       | **Kerahasiaan Dagang:** Kasir diharamkan melihat margin laba apotek.     |
| **Melihat Nama Pemasok/Distributor** | **DITUTUP**            | **Ya**       | Informasi relasi distributor khusus untuk manajerial gudang.             |
| **Katalog Lokasi Rak Fisik**         | **Ya**                 | Ya           | Membantu kasir menjawab pertanyaan letak obat tanpa buka keranjang.      |
| **Penerimaan Batch Masuk**           | Tidak                  | **Ya**       | Hanya logistik yang berhak mencocokkan fisik kardus obat dari PBF.       |
| **Karantina & Retur Obat ED**        | Tidak                  | **Ya**       | Hak eksklusif logistik untuk klaim finansial retur atau pemusnahan BPOM. |
| **Manipulasi Ambang Alert Stok**     | Tidak                  | **Ya**       | Wewenang pengaturan stok minimum berada pada manajer persediaan.         |
| **Cetak Ulang Struk Transaksi**      | **Ya (Milik Sendiri)** | Ya (Semua)   | Layanan cetak nota susulan jika pembeli meminta struk ulang.             |
| **Rekonsiliasi Kas (Tutup Shift)**   | **Input Fisik**        | **Auditor**  | Kasir menghitung uang fisik di laci (*blind close*), Admin memvalidasi.  |

4. SPESIFIKASI DETAIL SELURUH MODUL LAYAR

4.1 Modul 01: Pintu Gerbang Otentikasi (index.html)

  - Tujuan Pengguna: Halaman autentikasi aman bagi staf apotek sekaligus
    menyediakan akses instan bagi penguji/dosen saat sesi evaluasi.
  - Komponen & Tata Letak:
      - Latar belakang kanvas radial gelap (#1E293B ke #0F172A) yang memberikan
        atmosfer elegan.
      - Card Autentikasi Tengah: Berwarna putih solid (#FFFFFF) beradius 28px
        dengan bayangan tebal.
      - Formulir formal kredensial (Field Username, Field Kata Sandi, Tombol
        Masuk Sistem berlatar gelap pekat dengan teks putih).
      - Bilah Pintasan Evaluator (Demo Shortcuts): Dua kartu taktil 1-klik:
        1.  "Masuk sebagai Kasir" (Ikon keranjang hijau, mengarahkan ke
            kasir-pos.html).
        2.  "Masuk sebagai Admin Gudang" (Ikon boks logistik biru, mengarahkan
            ke admin-dashboard.html).
  - Micro-Interactions: Efek hover transform translateY(-2px) dengan aksen batas
    hijau toska saat kursor menyentuh kartu demo.

4.2 Modul 02: Meja Kasir Cerdas & FEFO Simulator (kasir-pos.html)

  - Tujuan Pengguna: Antarmuka titik penjualan (POS) berkecepatan tinggi yang
    mengeksekusi algoritma FEFO di backend tanpa menuntut kasir memilih nomor
    batch manual.
  - Komponen Tata Letak (Dual-Pane Cockpit):
      - Dynamic Island Nav: Kapsul navigasi melayang (Logo, Kasir POS [Aktif],
        Riwayat Shift, Cek Stok, Lencana Kasir: Denna A.).
      - Omni-Command HUD: Bilah pencarian terpusat berkontur kapsul dengan ikon
        kaca pembesar dan penanda pintasan ESC Clear.
      - Category Filter Chips Bar: Baris pil taktil horizontal (Semua Obat (8),
        Analgesik, Antibiotik, Sirup, Antihistamin, Lambung).
      - Kolom Kiri (Katalog Obat Siap Jual):
          - Grid kartu obat adaptif (menyesuaikan ukuran layar monitor).
          - Setiap kartu memuat: Kategori obat, Lencana sisa stok total, Nama
            paten obat, Bentuk sediaan, Indikator Batch FEFO Terdekat (Nomor
            batch & Tanggal ED), Harga jual eceran, dan tombol bulat +.
      - Kolom Kanan (Smart Companion Pane — Dual State):
          - Kondisi 1: Shift Companion State (Saat Keranjang Kosong):
            Menampilkan statistik shift aktif kasir (Total nota hari ini: 18
            transaksi, Omzet shift: Rp 845.000), Radar Barcode Scanner
            beranimasi detak (pulse) yang menandakan alat siap digunakan, serta
            daftar pintasan tombol kasir cepat. Ruang layar tidak pernah kosong
            melompong.
          - Kondisi 2: Active Cart State (Saat Obat Dipilih): Bertransisi halus
            (morphing) menjadi struk belanja interaktif. Menampilkan daftar item
            belanja, kontrol kuantitas + / -, kalkulator uang diterima,
            perhitungan kembalian otomatis, dan Kotak Transparansi Alokasi Batch
            FEFO.
  - Fitur Inovasi Inti:
      - Simulasi Real-Time Split-Batch: Jika kuantitas beli melebihi stok batch
        terdekat, lencana kuning SPLIT BATCH otomatis menyala, memperlihatkan
        pemecahan kuantitas antar-batch fisik secara transparan.
      - Anti-Overstock Hard Guardrail: Tombol + terkunci dan memunculkan
        notifikasi peringatan jika kuantitas belanja mencapai batas akumulasi
        seluruh batch fisik aktif yang ada di sistem.

4.3 Modul 03: Riwayat Transaksi Shift Kasir (kasir-riwayat.html)

  - Tujuan Pengguna: Tempat kasir memantau seluruh transaksi yang diterbitkan
    pada jam kerjanya, melihat pembagian pemotongan batch pada nota lampau, dan
    mencetak ulang struk fisik.
  - Komponen & Tata Letak:
      - Tiga kartu metrik ringkasan shift: Total Nota Diterbitkan (18 Struk),
        Akumulasi Omzet (Rp 845.000 terbagi atas Tunai & QRIS), dan Rata-rata
        Nilai Keranjang Belanja (Basket Size).
      - Tabel riwayat transaksi dengan kolom: Nomor Faktur (#INV-...), Waktu
        transaksi, Rincian item obat beserta detail nomor batch FEFO yang
        terpotong, Metode pembayaran, Total belanja, dan Tombol Aksi Cetak
        Ulang.
      - Modal Simulasi Thermal Printer: Pratinjau struk fisik berformat
        monospace khas kertas kasir apotek (Nama apotek, alamat, nomor nota,
        kasir bertugas, rincian barang, total bayar, kembalian, dan ucapan lekas
        sembuh).

4.4 Modul 04: Katalog Ketersediaan & Lokasi Rak (kasir-stok.html)

  - Tujuan Pengguna: Memfasilitasi kasir dalam menjawab pertanyaan ketersediaan
    obat dan menemukan titik simpan rak obat fisik di gudang/etalase secara
    cepat (Read-Only).
  - Komponen & Tata Letak:
      - Bilah pencarian cepat untuk memfilter nama obat, khasiat terapi, atau
        nomor rak fisik.
      - Tabel informatif yang memuat: Kode SKU, Nama obat, Bentuk sediaan, Titik
        Lokasi Rak Fisik (contoh: Rak A - Baris 2, Etalase Sirup - E3), Harga
        jual eceran konsumen, Status stok (Tersedia / Stok Tipis), dan Nomor
        batch prioritas FEFO terdekat.
      - Akses data harga beli modal dan distributor ditutup total.

4.5 Modul 05: Dashboard Eksekutif & Radar Kedaluwarsa (admin-dashboard.html)

  - Tujuan Pengguna: Pusat kendali manajer apotek dan petugas gudang untuk
    memantau pergerakan stok, valuasi modal, dan mitigasi risiko kedaluwarsa.
  - Komponen & Tata Letak:
      - Bento 4-Card Telemetri Eksekutif:
        1.  Valuasi Stok Fisik: Total nilai kapital modal obat yang tersimpan di
            gudang (Rp 48.650.000).
        2.  Batch Kritis (\le 30 Hari): Indikator bahaya merah berisi jumlah
            batch yang wajib segera dikeluarkan.
        3.  Waspada ED (31 - 90 Hari): Indikator peringatan kuning untuk
            mitigasi dini.
        4.  Efisiensi Pencegahan ED: Rasio persentase keberhasilan sistem
            mencegah obat kedaluwarsa (99.2%).
      - Hero Component: Visual Expiration Radar Queue:
          - Garis pita waktu (timeline track) bergradasi warna: Merah (Zona
            Kritis), Kuning (Zona Waspada), Hijau (Zona Aman).
          - Tabel antrean prioritas kedaluwarsa fisik yang diurutkan menaik
            berdasarkan TanggalKadaluwarsa ASC.
          - Memuat taksiran nilai modal tertahan pada batch bersangkutan serta
            lencana status tindakan (Prioritas 1 Kasir).

4.6 Modul 06: Penerimaan Batch & Faktur Logistik (admin-batch.html)

  - Tujuan Pengguna: Registrasi kedatangan kardus obat fisik dari distributor
    (PBF) berdasarkan dokumen faktur resmi pabrik.
  - Komponen & Tata Letak:
      - Formulir Surat Jalan PBF (Kolom Kiri):
          - Pilihan instansi distributor (PT. Kimia Farma, PT. Kalbe Farma, PT.
            Dexa Medica, dll).
          - Input Nomor Faktur / Surat Jalan resmi.
          - Pemilihan Katalog Master Obat.
          - Input Nomor Batch produksi pabrik.
          - Input Tanggal Kedaluwarsa fisik (berbatas garis merah tegas).
          - Input Kuantitas Masuk dan Harga Modal Beli per unit.
      - Log Penerimaan Batch Terkini (Kolom Kanan):
          - Tabel audit kedatangan barang hari ini yang langsung terintegrasi
            dengan status antrean FEFO.
  - Validasi Logika Khusus:
      - Pencegahan Kedaluwarsa Lampau (SRS FR-INV-02): Sistem memvalidasi
        tanggal kedaluwarsa secara real-time. Jika tanggal yang diinput sama
        dengan atau lebih lampau dari tanggal hari ini, formulir ditolak secara
        mutlak dengan notifikasi pelanggaran regulasi.

4.7 Modul 07: Karantina & Penanganan Obat ED (admin-karantina.html)

  - Tujuan Pengguna: Tata kelola pemisahan fisik obat kedaluwarsa dari peredaran
    umum secara sah sesuai regulasi BPOM tanpa merusak integritas basis data
    historis.
  - Komponen & Tata Letak:
      - Kartu indikator Batch Terisolasi (Locked), Estimasi Nilai Klaim Retur,
        dan Status Kepatuhan Audit Regulasi.
      - Tabel daftar batch berstatus kedaluwarsa dengan penanda lencana Diblir
        Kasir (Locked).
      - Dua Opsi Tindakan Hukum Farmasi:
        1.  Tombol "Retur PBF": Membuka modal penerbitan formulir surat
            pengembalian barang ke distributor rekanan untuk klaim nota
            kredit/penggantian produk baru.
        2.  Tombol "Musnahkan": Memicu alur pencatatan Pemusnahan Fisik resmi
            dengan konfirmasi audit untuk menerbitkan Berita Acara Pemusnahan
            (BAP).

4.8 Modul 08: Master Katalog & Pengaturan Ambang Stok (admin-katalog.html)

  - Tujuan Pengguna: Pengelolaan induk data obat dan penentuan ambang batas
    peringatan stok menipis (Minimum Stock Alert Threshold).
  - Komponen & Tata Letak:
      - Tabel master SKU obat lengkap dengan kategori terapi, bentuk sediaan,
        harga jual, ambang batas peringatan, dan status ketersediaan akumulatif
        fisik.
      - Modal Tambah Master Obat: Formulir penambahan SKU baru ke dalam database
        sebelum obat fisik diterima.
      - Modal Atur Ambang Batas: Formulir fleksibel untuk mengubah batas minimal
        stok per obat yang akan memicu indikator bahaya merah pada dashboard
        jika total stok fisik berada di bawah ambang tersebut.

5. SPESIFIKASI ALGORITMA & LOGIKA MESIN FEFO (CORE ENGINE)

5.1 Mekanisme Alokasi Pemotongan Batch (Split-Batch Logic)

Ketika kasir mengonfirmasi transaksi untuk suatu jenis obat O sejumlah
permintaan kuantitas Q_{\text{req}}:

[ Kasir Meminta Obat O dengan Jumlah Q_req ]
                     │
                     ▼
[ Query Database: Ambil seluruh batch aktif dari Obat O ]
  Kriteria: IdObat == O AND StokSisa > 0 AND TanggalKadaluwarsa > HariIni
  Urutan  : ORDER BY TanggalKadaluwarsa ASC
                     │
                     ▼
         [ Inisialisasi: SisaKebutuhan = Q_req ]
                     │
       ┌─────────────┴─────────────┐
       ▼                           ▼
[ Apakah SisaKebutuhan > 0? ]     [ Selesai ]
       │ YA                        │ TIDAK
       ▼                           ▼
[ Ambil Batch Terdepan (Batch_i) ] [ Commit Transaksi Database ]
       │
       ├─► KASUS A: StokSisa(Batch_i) >= SisaKebutuhan
       │     Alokasikan = SisaKebutuhan
       │     StokSisa(Batch_i) = StokSisa(Batch_i) - Alokasikan
       │     Catat DetailPenjualan(IdBatch_i, Alokasikan, Harga)
       │     SisaKebutuhan = 0
       │     (Selesai - Single Batch)
       │
       └─► KASUS B: StokSisa(Batch_i) < SisaKebutuhan
             Alokasikan = StokSisa(Batch_i)
             StokSisa(Batch_i) = 0 (Batch Habis)
             Catat DetailPenjualan(IdBatch_i, Alokasikan, Harga)
             SisaKebutuhan = SisaKebutuhan - Alokasikan
             Lencana "SPLIT BATCH" Aktif
             (Iterasi berlanjut ke Batch_i+1)

5.2 Integritas Transaksi Database (ACID Compliance)

Seluruh proses pengurangan multi-batch di atas dieksekusi di dalam satu blok
transaksi basis data:

using var transaction = await _context.Database.BeginTransactionAsync();
try {
    // 1. Eksekusi loop alokasi FEFO
    // 2. Simpan Header Penjualan
    // 3. Simpan seluruh baris Detail Penjualan
    // 4. Update stok fisik di tabel BatchObat
    await _context.SaveChangesAsync();
    await transaction.CommitAsync();
} catch (Exception) {
    await transaction.RollbackAsync(); // Batalkan seluruh alokasi jika terjadi kegagalan
    throw;
}

5.3 Protokol Karantina Otomatis (Zero-Tolerance Expired Sale)

Sistem memiliki logika filter global (Global Query Filter) di tingkat backend:
\text{StatusBatch} = \begin{cases} \text{AKTIF (Dijual Kasir)}, & \text{jika } \text{TanggalKadaluwarsa} > \text{TanggalHariIni} \\ \text{KARANTINA (Auto-Locked)}, & \text{jika } \text{TanggalKadaluwarsa} \le \text{TanggalHariIni} \end{cases}
Batch dengan status KARANTINA secara otomatis dihilangkan dari hasil query modul
kasir, sehingga kasir tidak akan pernah dapat melihat atau menjual obat yang
telah melewati tanggal kedaluwarsa.

6. PROTOKOL AUDIT SHIFT & REKONSILIASI KASIR

6.1 Alur Tutup Shift Kasir (Blind Close Protocol)

1.  Kasir menyelesaikan transaksi terakhir pada jam tugasnya, lalu menekan
    tombol "Rekap Tutup Shift" pada modul kasir-riwayat.html.
2.  Kasir menghitung fisik uang tunai yang ada di dalam laci kas (Cash Drawer).
3.  Kasir menginput nominal fisik uang tunai tanpa diberi tahu berapa jumlah
    uang tunai yang tercatat oleh sistem (Blind Balancing guna mencegah
    kecurangan pengisian angka palsu).
4.  Sistem menerbitkan struk serah terima shift sementara bertanda tangan kasir.

6.2 Alur Verifikasi & Audit oleh Admin Gudang

1.  Admin Gudang/Supervisor membuka modul rekonsiliasi kasir pada hak akses
    Admin.
2.  Sistem mengomparasi data secara otomatis:
    \Delta \text{Kas} = \text{Nominal Fisik Kasir} - \text{Nominal Penjualan Tunai Sistem}
3.  Hasil rekonsiliasi dikategorikan menjadi dua kondisi:
      - MATCH (\Delta \text{Kas} = 0): Seluruh uang tunai fisik seimbang dengan
        sistem. Status shift disahkan (Approved).
      - DISCREPANCY (\Delta \text{Kas} \neq 0): Terdapat selisih kas
        (minus/plus). Sistem otomatis membekukan laporan shift dan menerbitkan
        Berita Acara Selisih Kas yang wajib ditandatangani kasir bersangkutan.
4.  Admin melakukan verifikasi fisik acak (spot-check) pada nomor batch fisik di
    rak apotek berdasarkan log pemotongan FEFO pada shift tersebut.

7. PANDUAN IMPLEMENTASI TEKNIS KE C# ASP.NET CORE MVC (.NET 8)

7.1 Pemetaan Berkas HTML Prototipe ke Komponen ASP.NET Core

| Berkas Prototipe HTML  | Komponen Target di ASP.NET Core MVC | Controller Bersesuaian | ViewModel / Model Data           |
| :--------------------- | :---------------------------------- | :--------------------- | :------------------------------- |
| `index.html`           | `Views/Account/Login.cshtml`        | `AccountController.cs` | `LoginViewModel.cs`              |
| `kasir-pos.html`       | `Views/Kasir/Index.cshtml`          | `KasirController.cs`   | `PosTransactionViewModel.cs`     |
| `kasir-riwayat.html`   | `Views/Kasir/RiwayatShift.cshtml`   | `KasirController.cs`   | `ShiftSummaryViewModel.cs`       |
| `kasir-stok.html`      | `Views/Kasir/CekStok.cshtml`        | `KasirController.cs`   | `KatalogStokViewModel.cs`        |
| `admin-dashboard.html` | `Views/Admin/Dashboard.cshtml`      | `AdminController.cs`   | `ExecutiveDashboardViewModel.cs` |
| `admin-batch.html`     | `Views/Gudang/Penerimaan.cshtml`    | `GudangController.cs`  | `BatchReceiptViewModel.cs`       |
| `admin-karantina.html` | `Views/Gudang/Karantina.cshtml`     | `GudangController.cs`  | `QuarantineAuditViewModel.cs`    |
| `admin-katalog.html`   | `Views/Admin/MasterObat.cshtml`     | `AdminController.cs`   | `MasterObatViewModel.cs`         |
| `assets/css/style.css` | `wwwroot/css/pharmasentinel.css`    | *Asset Statis Web*     | *Global Design System Tokens*    |

7.2 Rencana Struktur Arsitektur Solution Visual Studio (.NET 8)

PharmaSentinel.sln
├── src/
│   ├── PharmaSentinel.Web/             # ASP.NET Core MVC (Controllers, Views, wwwroot)
│   ├── PharmaSentinel.Core/            # Domain Entities, Enums, & Interfaces
│   │   ├── Entities/ (Obat, BatchObat, Penjualan, DetailPenjualan, Pengguna, Pemasok)
│   │   └── Interfaces/ (IFEFOEngineService, IInventoryService, ITransactionService)
│   ├── PharmaSentinel.Services/        # Business Logic Implementations
│   │   ├── FEFOEngineService.cs        # Implementasi Logika Algoritma Split-Batch FEFO
│   │   ├── InventoryService.cs         # Validasi Kedaluwarsa & Alert Ambang Stok
│   │   └── ReconciliationService.cs    # Logika Audit Selisih Kasir & Tutup Shift
│   └── PharmaSentinel.Infrastructure/  # Entity Framework Core & MySQL Access
│       ├── Data/ (AppDbContext.cs, Migrations/)
│       └── Repositories/
└── tests/
    └── PharmaSentinel.Tests/           # Unit Testing Algoritma FEFO (XUnit)

8. KESIMPULAN & TAHAP BERIKUTNYA

Dokumen Cetak Biru UI/UX ini telah membekukan (design freeze) seluruh rancangan
tata letak, alur interaksi pengguna, skema warna aksesibilitas tinggi, serta
arsitektur logika bisnis sistem inventaris berbasis FEFO.

Dengan tuntasnya prototipe antarmuka mandiri ini, tim pengembang memiliki acuan
visual dan perilaku sistem yang baku untuk memasuki Tahap Implementasi Backend
C# ASP.NET Core, integrasi basis data MySQL melalui Entity Framework Core, dan
pengujian keandalan algoritma pemotongan batch secara atomik.


---
