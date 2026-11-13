# Data Tugas 1

## Indonesia Wikipedia Pages

| Item | Keterangan |
|---|---|
| Sumber | [Indonesia Wikipedia Pages, diunggah oleh greegtitan di Kaggle](https://www.kaggle.com/datasets/greegtitan/indonesia-wikipedia-pages) |
| Lisensi dataset di Kaggle | [CC BY-SA 3.0](https://creativecommons.org/licenses/by-sa/3.0/) |
| File yang digunakan | `wikipedia_id-raw.csv` |
| Ukuran CSV | 3.010.074.263 byte, sekitar 3,01 GB |
| Isi CSV | 1.550.872 record dan 6 kolom |
| Unit analisis | Satu record halaman Wikipedia bahasa Indonesia, termasuk halaman pengalihan |
| Rentang timestamp teramati | 19 April 2004 13:00:14 hingga 20 November 2022 10:49:11 UTC |

Rentang di atas berasal dari nilai minimum dan maksimum kolom `timestamp` yang diperiksa di [Notebook 1](../notebooks/01_data_profiling.ipynb).

| Kolom | Isi |
|---|---|
| `id` | Pengenal record yang dipakai sebagai kunci deduplikasi |
| `parent_id` | ID referensi yang dapat bernilai kosong |
| `timestamp` | Waktu yang tercatat pada record dalam format ISO UTC |
| `hash` | Nilai hash yang disediakan di dataset |
| `title` | Judul halaman |
| `text` | Teks sumber halaman, termasuk markup Wikipedia |

## Cara memperoleh data

1. Buka [halaman file dataset di Kaggle](https://www.kaggle.com/datasets/greegtitan/indonesia-wikipedia-pages/data?select=wikipedia_id-raw.csv).
2. Unduh `wikipedia_id-raw.csv`. Jika unduhan berbentuk arsip, ekstrak CSV dari arsip tersebut.
3. Letakkan CSV di `data/raw/wikipedia_id-raw.csv` dengan nama aslinya. Buat folder `data/raw/` jika belum ada. Ukuran CSV yang sudah diekstrak sekitar 3,01 GB.
4. Ikuti [panduan Docker dan JupyterLab](../README.md#menjalankan-proyek-dengan-docker) untuk menjalankan notebook secara berurutan.

## Data hasil pengolahan

[Notebook 2](../notebooks/02_data_cleaning.ipynb) membuat `data/processed/wikipedia_id-bersih.parquet` dengan 1.550.872 record, enam kolom asal, dan kolom tambahan `panjang_teks`. Kolom tambahan ini dibatasi pada 3.506 karakter saat cleaning. [Notebook 3](../notebooks/03_eda_and_insights.ipynb) menghitung ulang panjang asli dari `text` untuk analisis distribusi.

Simpan CSV mentah di `data/raw/` dan hasil pengolahan di `data/processed/`. Kedua folder data diabaikan oleh Git sehingga file besar tetap disimpan secara lokal.
