# Project Data Warehouse (DWH) Transjakarta

Project ini berfokus pada pembangunan infrastruktur Data Warehouse menggunakan **Star Schema** untuk data operasional Transjakarta. Data yang digunakan bersumber dari data GTFS (General Transit Feed Specification), data internal operasional, dan data rating halte dari Google Maps API.

## 1. Prasyarat (Prerequisites)

Sebelum memulai, pastikan Anda memiliki:
- **Python 3.8+** terinstal di sistem Anda.
- Akun **Google Cloud Platform (GCP)** dengan project yang aktif.
- Akses ke **BigQuery** dan file **Service Account Key** (`credentials.json`) dengan role *BigQuery Admin* atau *Editor*.

---

## 2. Setup Environment

Ikuti langkah-langkah berikut untuk menyiapkan lingkungan kerja:

### A. Clone atau Siapkan Folder Project
Pastikan folder project Anda memiliki struktur berikut:
```text
project-dwh/
├── notebook.ipynb              # File utama notebook Anda
├── credentials.json            # File kunci API dari Google Cloud
├── stops.txt                   # Data GTFS
├── stop_times.txt              # Data GTFS
├── routes.txt                  # Data GTFS
├── trips.txt                   # Data GTFS
├── calendar.txt                # Data GTFS
├── transjakarta_synthetic.csv  # Data rating Google Maps
└── Data Operasional...csv      # Data manual tarif
```

### B. Install Dependencies
Buka terminal atau command prompt, lalu jalankan perintah berikut untuk menginstal semua library yang dibutuhkan:

```bash
pip install pandas numpy matplotlib seaborn google-cloud-bigquery dbml
```

---

## 3. Persiapan Google BigQuery

1. Buka https://console.cloud.google.com/
2. Buat **Project** baru (jika belum ada) dan catat **Project ID**-nya.
3. Di menu BigQuery, buat sebuah **Dataset** baru:
   - **Dataset ID**: `transjakarta_dwh`
   - **Data Location**: `asia-southeast2` (Jakarta) atau lokasi yang Anda inginkan.
4. Letakkan file `credentials.json` hasil download dari IAM Service Account ke dalam folder project Anda.

---

## 4. Cara Menjalankan Notebook

Notebook dibagi menjadi beberapa tahap (B1 - B3) sesuai alur ETL:

### Tahap 1: Extract (B1)
- Jalankan cell pertama untuk me-load data dari file lokal ke dalam Pandas DataFrame.
- Pastikan semua file `.txt` dan `.csv` terbaca tanpa error.

### Tahap 2: Transform (B2)
- Jalankan cell transformasi untuk membersihkan data (*handle missing values*).
- Pada tahap ini, waktu GTFS akan distandarisasi, nama halte diseragamkan, dan kolom metrik baru seperti `headway_minutes` serta `hour_bin` akan dibuat secara otomatis.
- Data akan dipecah menjadi 1 Tabel Fakta (`fact_trip_schedules`) dan 4 Tabel Dimensi.

### Tahap 3: Load ke BigQuery (B3)
- **PENTING**: Ubah variabel `PROJECT_ID` dan `DATASET_ID` pada kode sesuai dengan ID milik Anda.
- Jalankan cell Load untuk mengirimkan data dari Python ke Google BigQuery Cloud.

### Tahap 4: Visualisasi & Analysis
- Jalankan cell visualisasi satu per satu (Heatmap, Scatter, Bar Chart, dan Map).
- Gunakan query SQL yang disediakan di bagian akhir notebook untuk melakukan verifikasi data langsung di konsol BigQuery.

---

## 5. Metadata Schema DWH

- **Fact Table**: `fact_trip_schedules` (Grain: Per jadwal kedatangan bus per halte).
- **Dimensi**:
  - `dim_stop`: Informasi halte dan rating.
  - `dim_route`: Informasi rute/koridor.
  - `dim_time`: Atribut waktu, jam, dan tipe hari.
  - `dim_operator`: Informasi pengelola moda.

---

## Kontribusi
Project ini dikembangkan untuk tujuan analisis data transportasi publik di Jakarta. Silakan kembangkan rute analisis baru sesuai kebutuhan bisnis Anda.
