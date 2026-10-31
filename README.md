# Deteksi dan Analisis File Duplikat

Project ini menyediakan dua notebook Python untuk menemukan file yang memiliki
nama sama, menganalisis hasilnya, dan membuat rekomendasi file yang perlu
ditinjau. Pemindaian mencakup sebuah drive atau folder beserta seluruh
subfoldernya.

## Fitur

### Pemindaian

- Memindai folder secara rekursif menggunakan `os.walk`.
- Memeriksa semua format file.
- Menganggap perbedaan huruf besar/kecil sebagai nama yang sama secara
  default. Contoh: `Laporan.pdf` dan `laporan.PDF`.
- Menampilkan ringkasan jumlah file untuk setiap nama duplikat.
- Menampilkan detail nama, path lengkap, ukuran dalam byte, dan ukuran dalam
  MB.
- Mengurutkan hasil detail dari file terbesar.
- Mencatat file atau folder yang tidak dapat diakses tanpa menghentikan proses
  pemindaian.
- Mengekspor hasil detail ke CSV dengan encoding `utf-8-sig`.

### Analisis hasil

- Melakukan exploratory data analysis (EDA) terhadap hasil pemindaian.
- Menganalisis ekstensi, folder, ukuran, dan jumlah salinan nama file.
- Menampilkan visualisasi distribusi ukuran dan total ukuran berdasarkan
  ekstensi.
- Memprioritaskan arsip `.zip` dan `.rar`, serta format archive umum lainnya.
- Menghasilkan rekomendasi `TINJAU PRIORITAS TINGGI`, `TINJAU`, atau
  `REFERENSI`.
- Mengekspor rekomendasi ke CSV untuk ditinjau sebelum mengambil tindakan.
- Menggunakan urutan prioritas ukuran file terbesar, lalu jumlah file dengan
  nama sama.

## Persyaratan

- Python 3.9 atau lebih baru
- Jupyter Notebook, JupyterLab, atau VS Code
- Paket `pandas` dan `matplotlib`

Instalasi dependensi:

```bash
python -m pip install pandas matplotlib jupyter
```

## Struktur Notebook

| Notebook | Fungsi |
| --- | --- |
| [`deteksi_nama_file_identik.ipynb`](./deteksi_nama_file_identik.ipynb) | Memindai folder dan menemukan nama file duplikat |
| [`olah_hasil_deteksi_csv.ipynb`](./olah_hasil_deteksi_csv.ipynb) | EDA, visualisasi, dan rekomendasi prioritas dari hasil pemindaian |

## Alur Penggunaan

### 1. Memindai folder

1. Buka [`deteksi_nama_file_identik.ipynb`](./deteksi_nama_file_identik.ipynb).
2. Pada cell konfigurasi, ubah `TARGET_DIR` ke drive atau folder yang ingin
   diperiksa:

   ```python
   TARGET_DIR = Path("D:/")
   ```

   Contoh folder tertentu:

   ```python
   TARGET_DIR = Path(r"D:\Dokumen")
   ```

3. Atur opsi perbandingan nama:

   ```python
   CASE_INSENSITIVE = True
   ```

   Gunakan `False` jika kapitalisasi harus dianggap berbeda.

4. Jalankan cell secara berurutan.
5. Untuk menyimpan hasil ke CSV, pastikan pengaturan berikut aktif:

   ```python
   EXPORT_CSV = True
   OUTPUT_CSV = Path.cwd() / "hasil_deteksi_nama_file_identik.csv"
   ```

   File CSV akan disimpan di folder kerja notebook. CSV sumber menggunakan
   separator koma.

### 2. Menganalisis CSV

1. Pastikan `hasil_deteksi_nama_file_identik.csv` berada di folder project.
2. Buka [`olah_hasil_deteksi_csv.ipynb`](./olah_hasil_deteksi_csv.ipynb).
3. Jalankan cell secara berurutan.
4. Tinjau tabel rekomendasi dan visualisasi yang dihasilkan.

Hasil analisis diekspor dengan separator pipe (`|`) agar aman ketika nama file
atau path mengandung koma. Jika format CSV sumber berubah, sesuaikan
`INPUT_SEP` pada cell konfigurasi notebook.

Notebook analisis membaca kolom berikut:

| Kolom | Keterangan |
| --- | --- |
| `nama_file` | Nama file |
| `path_lengkap` | Lokasi lengkap file |
| `ukuran_byte` | Ukuran file dalam byte |
| `ukuran_MB` | Ukuran file dalam MB |

## Skor Prioritas Penghapusan

Skor adalah heuristik untuk membantu review manual, bukan keputusan
penghapusan otomatis. Urutan tabel rekomendasi tidak hanya berdasarkan skor:
file diurutkan berdasarkan ukuran terbesar terlebih dahulu, kemudian jumlah
file dengan nama sama. Dengan aturan ini, file berukuran gigabyte akan muncul
lebih dulu daripada file JavaScript kecil yang memiliki banyak salinan.

- `.zip` atau `.rar`: **+80**
- Archive lain (`.7z`, `.tar`, `.gz`, `.bz2`, `.xz`, `.tgz`, `.tbz2`): **+60**
- Setiap salinan tambahan dengan nama sama: **+10**, maksimum **+30**
- File minimal 1 GB: **+10**
- File minimal 5 GB: **+20**

Label yang dihasilkan:

| Label | Arti |
| --- | --- |
| `TINJAU PRIORITAS TINGGI` | Skor minimal 60; mulai review dari sini |
| `TINJAU` | Skor 30–59; perlu pemeriksaan manual |
| `REFERENSI` | Skor di bawah 30; bukan prioritas utama |

## Hasil

Notebook pemindaian menampilkan dua tabel:

### Ringkasan (`summary_df`)

| Kolom | Keterangan |
| --- | --- |
| `nama_file` | Nama file duplikat |
| `jumlah` | Jumlah file dengan nama tersebut |

### Detail (`detail_df`)

| Kolom | Keterangan |
| --- | --- |
| `nama_file` | Nama file |
| `path_lengkap` | Lokasi lengkap file |
| `ukuran_byte` | Ukuran file dalam byte |
| `ukuran_MB` | Ukuran file dalam MB |

Notebook analisis menghasilkan:

- `rekomendasi_prioritas_penghapusan.csv`: daftar file dengan skor dan label
  rekomendasi, dipisahkan dengan `|`.
- `ringkasan_eda_file_duplikat.csv`: jumlah file berdasarkan label
  rekomendasi, dipisahkan dengan `|`.

Semua file CSV diabaikan oleh Git melalui [`.gitignore`](./.gitignore) agar
hasil pemeriksaan lokal tidak terikut ke repository.

## Catatan

- Pemindaian seluruh drive dapat membutuhkan waktu lama, terutama pada drive
  dengan banyak file.
- Pastikan pengguna yang menjalankan notebook memiliki izin membaca folder
  target.
- Jumlah item yang gagal diakses ditampilkan pada output sebagai
  `scan_errors`, tetapi detail error tidak diekspor ke CSV.
- Notebook hanya mendeteksi kesamaan nama file, bukan kesamaan isi file.
- Notebook analisis tidak menghapus, memindahkan, atau mengubah file asli.
- Sebelum menghapus file secara manual, pastikan file bukan satu-satunya
  salinan, bukan bagian dari backup, dan tidak sedang digunakan.
