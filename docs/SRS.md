# Software Requirements Specification (SRS)
## Proyek: Sistem Informasi Penjualan Apotek (Algoritma FEFO)
**Versi:** 1.0  
**Target Pengguna:** Apotek Skala Kecil hingga Menengah  
**Teknologi:** C# (.NET 8), ASP.NET Core MVC, Entity Framework Core, MySQL  

---

### 1. Pendahuluan
#### 1.1 Latar Belakang
Manajemen inventaris obat merupakan aspek paling krusial dalam operasional sebuah apotek. Berbeda dengan toko ritel umum, obat memiliki batas masa simpan (*Expiration Date / ED*). Apotek konvensional sering mengalami kerugian akibat obat kedaluwarsa yang terlewat di rak gudang, atau kelalaian kasir yang menjual batch baru sementara batch lama masih menumpuk.

Metode **First Expired, First Out (FEFO)** memastikan bahwa stok obat yang memiliki tanggal kedaluwarsa paling dekat harus dikeluarkan/dijual terlebih dahulu. Mengingat beban kerja kasir yang tinggi, proses pemilihan batch ini tidak dapat dibebankan pada pengecekan fisik manual. Oleh karena itu, diperlukan sistem informasi berbasis web yang mengintegrasikan kasir *Point of Sale* (POS) dengan backend cerdas yang secara otomatis mengalokasikan stok berdasarkan prinsip FEFO.

#### 1.2 Rumusan Masalah
1. Bagaimana mengotomatisasi pengurangan stok dari batch dengan tanggal kedaluwarsa paling dekat saat transaksi kasir berlangsung?
2. Bagaimana menangani skenario saat satu pesanan obat harus mengambil dari dua batch berbeda akibat stok batch pertama tidak mencukupi (*split-batch*)?
3. Bagaimana memberikan peringatan dini (*early warning*) atas obat yang mendekati kedaluwarsa dan stok yang menipis?

#### 1.3 Tujuan & Manfaat
- **Tujuan:** Membangun aplikasi kasir apotek yang meminimalisir risiko obat *expired* melalui otomasi alokasi batch berbasis algoritma FEFO.
- **Manfaat:** Mencegah kerugian finansial apotek, menjamin keamanan pasien dari bahaya obat kedaluwarsa, dan mempercepat proses layanan di meja kasir.

---

### 2. Deskripsi Arsitektur & Lingkungan Sistem
* **Pola Arsitektur:** Model-View-Controller (MVC) berbasis ASP.NET Core.
* **Database & ORM:** MySQL 8.x dengan Pomelo Entity Framework Core.
* **Front-end:** Razor Views, HTML5/CSS3, Bootstrap 5, Vanilla JavaScript.
* **Aktor Sistem:**
  1. **Administrator / Pengelola Gudang:** Mengelola katalog obat, kategori, pemasok, menginput kedatangan batch obat baru, serta memantau laporan analitik.
  2. **Kasir:** Melakukan transaksi penjualan obat dengan antarmuka yang cepat dan terautomasi.

---

### 3. Logika & Mekanisme Algoritma FEFO (Spesifikasi Inti)

Ketika kasir memilih suatu obat $O$ dan memasukkan jumlah beli $Q$:
1. Sistem melakukan pencarian batch aktif pada database dengan kriteria:
   $$\text{IdObat} = O \quad \land \quad \text{StokSisa} > 0 \quad \land \quad \text{TanggalKadaluwarsa} > \text{HariIni}$$
2. Hasil query diurutkan secara menaik:
   $$\text{ORDER BY TanggalKadaluwarsa ASC}$$
3. Sistem menginisiasi iterasi terhadap daftar batch:
   - Jika $\text{StokSisa} \ge Q$: Alokasikan seluruh $Q$ dari batch ini. Kurangi stok batch sebanyak $Q$. Catat transaksi pada `DetailPenjualan`. Iterasi selesai.
   - Jika $\text{StokSisa} < Q$: Ambil seluruh sisa stok batch tersebut ($Q_{\text{ambil}} = \text{StokSisa}$). Set stok batch menjadi 0. Catat detail transaksi untuk batch ini. Hitung sisa kebutuhan $Q = Q - Q_{\text{ambil}}$, lalu lanjut ke batch berikutnya.
4. Seluruh proses pengurangan stok dan pencatatan transaksi wajib dibungkus dalam **Database Transaction (`DbContext.Database.BeginTransaction()`)**. Jika stok total dari semua batch tidak mencukupi atau terjadi kegagalan jaringan, transaksi otomatis di-*Rollback*.

---

### 4. Kebutuhan Fungsional (Functional Requirements)

#### Modul 1: Manajemen Master Data
* **FR-MTR-01 (Kategori & Obat):** Sistem memungkinkan Admin menambah, mengubah, dan menonaktifkan data obat beserta atribut kode obat, nama, bentuk sediaan (tablet, sirup, dll), harga jual, dan batas stok minimum.
* **FR-MTR-02 (Pemasok):** Sistem mencatat data distributor/pemasok obat (nama, telepon, alamat).

#### Modul 2: Inventaris & Manajemen Batch
* **FR-INV-01 (Input Batch Masuk):** Admin mencatat obat masuk dengan menyertakan: ID Obat, ID Pemasok, Nomor Batch pabrik, Tanggal Masuk, Tanggal Kedaluwarsa, Jumlah Stok, dan Harga Beli.
* **FR-INV-02 (Pencegahan ED Lewat Waktu):** Sistem menolak pencatatan batch baru jika tanggal kedaluwarsa yang dimasukkan sama dengan atau lebih lampau dari hari ini.

#### Modul 3: Transaksi Kasir (Point of Sale)
* **FR-POS-01 (Pencarian Obat Cepat):** Kasir dapat mencari obat berdasarkan Nama atau Kode Obat. Sistem menampilkan total stok gabungan dari semua batch aktif yang valid.
* **FR-POS-02 (Eksekusi FEFO Otomatis):** Sistem mengeksekusi algoritma FEFO di backend saat kasir mengonfirmasi pembayaran, tanpa menuntut kasir memilih nomor batch secara manual.
* **FR-POS-03 (Split Batch Multi-Detail):** Jika transaksi melibatkan lebih dari 1 batch, sistem membuat beberapa baris rekaman pada `DetailPenjualan` dengan ID batch yang bersesuaian.

#### Modul 4: Sistem Peringatan & Laporan
* **FR-RPT-01 (Early Warning Kedaluwarsa):** Menampilkan daftar batch obat yang akan kedaluwarsa dalam rentang $\le 90$ hari ke depan.
* **FR-RPT-02 (Alert Stok Menipis):** Memberi indikator visual merah jika total stok obat berada di bawah nilai `stok_minimum_alert`.
* **FR-RPT-03 (Rekapitulasi Penjualan):** Menampilkan ringkasan pendapatan harian, bulanan, dan margin laba kotor.

---

### 5. Kebutuhan Non-Fungsional (Non-Functional Requirements)
* **NFR-01 (Integritas ACID):** Transaksi pemotongan stok multi-batch bersifat atomik (semua tersimpan atau batalkan seluruhnya).
* **NFR-02 (Performa Respons):** Proses eksekusi kasir dan kalkulasi algoritma FEFO per transaksi harus diselesaikan dalam waktu kurang dari 1,5 detik.
* **NFR-03 (Keamanan Autentikasi):** Kata sandi pengguna wajib dienkripsi searah menggunakan *hash* (BCrypt / ASP.NET Identity Hashing). Kasir tidak diizinkan mengakses menu manipulasi master data obat.

---

### 6. Desain Kamus Data (Database Schema)

| Tabel | Kolom Utama | Tipe Data | Keterangan |
| :--- | :--- | :--- | :--- |
| **Kategori** | `id_kategori` | INT (PK, Auto) | ID Kategori Obat |
| | `nama_kategori` | VARCHAR(50) | Nama kategori obat |
| **Pemasok** | `id_pemasok` | INT (PK, Auto) | ID Distributor |
| | `nama_pemasok` | VARCHAR(100) | Nama instansi pemasok |
| **Obat** | `id_obat` | INT (PK, Auto) | ID Master Obat |
| | `id_kategori` | INT (FK) | Relasi ke tabel Kategori |
| | `kode_obat` | VARCHAR(20) | Kode unik/Barcode obat |
| | `nama_obat` | VARCHAR(100) | Nama paten / generik |
| | `harga_jual` | DECIMAL(12,2) | Harga jual ke konsumen |
| | `stok_minimum_alert` | INT | Ambang batas notifikasi stok |
| **Batch_Obat** | `id_batch` | INT (PK, Auto) | ID Unik Batch |
| | `id_obat` | INT (FK) | Relasi ke master obat |
| | `id_pemasok` | INT (FK) | Asal distributor obat |
| | `nomor_batch` | VARCHAR(50) | Nomor produksi pabrik |
| | `tanggal_kadaluwarsa` | DATE | **Kunci Sorting Algoritma FEFO** |
| | `stok_sisa` | INT | Stok aktual pada batch ini |
| **Pengguna** | `id_pengguna` | INT (PK, Auto) | ID Akun |
| | `peran` | VARCHAR(20) | Hak akses: Admin / Kasir |
| **Penjualan** | `id_penjualan` | INT (PK, Auto) | ID Transaksi (Header) |
| | `id_pengguna` | INT (FK) | Kasir yang bertugas |
| | `no_faktur` | VARCHAR(50) | Nomor invoice |
| | `total_bayar` | DECIMAL(12,2) | Total nilai belanja |
| **Detail_Penjualan** | `id_detail` | INT (PK, Auto) | ID Detail Baris Item |
| | `id_penjualan` | INT (FK) | Relasi ke invoice |
| | `id_batch` | INT (FK) | **Relasi langsung ke Batch (FEFO)** |
| | `jumlah` | INT | Kuantitas yang dipotong dari batch ini |
