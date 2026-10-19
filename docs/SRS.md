# Software Requirements Specification (SRS)
## Proyek: Sistem Informasi Penjualan Apotek (FEFO Engine)

### 1. Pendahuluan
#### 1.1 Latar Belakang
Manajemen inventaris obat di apotek memiliki risiko kerugian finansial tinggi apabila obat melewati masa kedaluwarsa sebelum terjual. Penerapan metode First Expired, First Out (FEFO) secara manual rentan terhadap kelalaian manusia (human error). Sistem ini dibangun untuk mengotomatisasi alokasi batch obat saat proses penjualan berlangsung.

#### 1.2 Tujuan
Membangun sistem informasi apotek berbasis web yang mampu mencatat transaksi kasir sekaligus melacak dan memotong stok batch obat dengan tanggal kedaluwarsa paling awal.

---

### 2. Deskripsi Sistem
* **Platform:** Web Application (ASP.NET Core MVC)
* **Database:** MySQL
* **Aktor:**
  1. **Admin/Gudang:** Mengelola data master obat, supplier, serta penerimaan batch obat baru.
  2. **Kasir:** Melayani transaksi penjualan obat kepada pelanggan.

---

### 3. Kebutuhan Fungsional (Functional Requirements)

* **FR-01: Manajemen Master Data**
  * Sistem menyediakan fungsi CRUD untuk data Kategori, Satuan/Sediaan, Obat, dan Pemasok.
  * Sistem memungkinkan penentuan batas minimum stok untuk notifikasi.

* **FR-02: Manajemen Batch & Kedaluwarsa**
  * Sistem mencatat setiap obat yang masuk berdasarkan Nomor Batch, Tanggal Masuk, Tanggal Kedaluwarsa, dan Stok Batch.
  * Satu jenis obat dapat memiliki banyak batch dengan tanggal kedaluwarsa berbeda.

* **FR-03: Mesin Penjualan FEFO (First Expired First Out)**
  * Kasir memilih nama obat dan memasukkan jumlah yang ingin dibeli.
  * Sistem backend secara otomatis mencari batch aktif dengan tanggal kedaluwarsa terdekat yang masih memiliki stok.
  * Jika jumlah pesanan melebihi stok di batch pertama, sistem secara otomatis mengambil sisa kebutuhan dari batch berikutnya (split-batch).

* **FR-04: Sistem Peringatan Dini (Early Warning System)**
  * Sistem menampilkan indikator obat yang mendekati tanggal kedaluwarsa (misal <= 90 hari).
  * Sistem menampilkan indikator obat yang stok totalnya berada di bawah batas minimum.

* **FR-05: Pelaporan**
  * Laporan rekapitulasi penjualan harian dan bulanan.
  * Laporan riwayat batch obat dan obat yang berstatus *expired*.

---

### 4. Kebutuhan Non-Fungsional (Non-Functional Requirements)
* **Integritas Data (ACID):** Transaksi pemotongan stok multi-batch dibungkus dalam mekanisme database transaction agar tidak terjadi inkonsistensi jika koneksi terputus.
* **Keamanan:** Password pengguna disimpan dalam format *hashing* aman.
