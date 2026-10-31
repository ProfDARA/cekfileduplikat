# Deteksi Nama File Identik

Notebook Python untuk menemukan file yang memiliki nama sama di dalam sebuah
drive atau folder, termasuk seluruh subfoldernya. Hasil pemeriksaan
menampilkan lokasi lengkap dan ukuran setiap file duplikat, serta dapat
disimpan ke CSV.

## Fitur

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

## Persyaratan

- Python 3.9 atau lebih baru
- Jupyter Notebook atau JupyterLab
- Paket `pandas`

Instalasi dependensi:

```bash
python -m pip install pandas jupyter
```

## Cara Menggunakan

1. Buka `deteksi_nama_file_identik.ipynb` menggunakan Jupyter Notebook,
   JupyterLab, atau VS Code.
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

   File CSV akan disimpan di folder kerja notebook.

## Hasil

Notebook menampilkan dua tabel:

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

File hasil default adalah `hasil_deteksi_nama_file_identik.csv`. File CSV
diabaikan oleh Git melalui `.gitignore` agar hasil pemeriksaan lokal tidak
terikut ke repository.

## Catatan

- Pemindaian seluruh drive dapat membutuhkan waktu lama, terutama pada drive
  dengan banyak file.
- Pastikan pengguna yang menjalankan notebook memiliki izin membaca folder
  target.
- Jumlah item yang gagal diakses ditampilkan pada output sebagai
  `scan_errors`, tetapi detail error tidak diekspor ke CSV.
- Notebook hanya mendeteksi kesamaan nama file, bukan kesamaan isi file.
