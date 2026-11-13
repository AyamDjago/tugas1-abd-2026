# Tugas 1: Eksplorasi dan Analisis Dataset Besar Indonesia

Proyek Analisis Big Data 2026 ini mengolah **1.550.872 record halaman Wikipedia bahasa Indonesia** dari file CSV berukuran **3,01 GB**. Analisis melihat sebaran waktu yang tercatat pada dataset serta perbedaan panjang teks menurut jenis halaman. Sumber, skema, dan petunjuk unduh ada di [dokumentasi data](data/README.md).

## Alur analisis

| Notebook | Proses dan keluaran |
|---|---|
| [01 — Data Profiling](notebooks/01_data_profiling.ipynb) | Memeriksa skema CSV, nilai kosong, keunikan ID, dan rentang timestamp menggunakan Polars serta DuckDB `SUMMARIZE`. |
| [02 — Data Cleaning](notebooks/02_data_cleaning.ipynb) | Menangani nilai kosong dan pencilan panjang teks, memeriksa duplikasi berdasarkan ID, lalu memvalidasi data dengan DuckDB. Hasilnya adalah Parquet dengan 1.550.872 record dan 7 kolom. |
| [03 — EDA and Insights](notebooks/03_eda_and_insights.ipynb) | Membaca Parquet, mengelompokkan record per bulan dengan Polars `group_by_dynamic()`, lalu menyajikan rata-rata bergerak tiga bulan, enam grafik interaktif Plotly, dan lima insight. |

## Temuan utama

- **31,6% record** memiliki timestamp pada 2022, dengan cakupan sampai 20 November UTC. Perbandingan Januari–Oktober menunjukkan 441.673 record pada 2022 dan 222.313 pada 2021.
- **39,3% record** terdeteksi sebagai halaman pengalihan berdasarkan awalan `#ALIH` atau `#REDIRECT`. Median panjang teks sumber adalah 34 karakter untuk pengalihan dan 1.089 karakter untuk kelompok halaman lain.
- Korelasi Spearman antara panjang judul dan teks adalah **0,01** pada data gabungan yang dianalisis, **0,62** pada halaman pengalihan, dan **−0,43** pada kelompok halaman lain.

Analisis waktu menghitung record berdasarkan timestamp yang tersedia. Panjang teks dihitung dari teks sumber, termasuk markup Wikipedia. Metode dan grafik pendukung tersedia di [Notebook 3](notebooks/03_eda_and_insights.ipynb).

## Menjalankan proyek dengan Docker

1. Nyalakan Docker Desktop. Jika folder proyek belum ada, clone fork ini. Jika sudah ada, buka PowerShell di folder proyek.

   ```powershell
   git clone https://github.com/AyamDjago/tugas1-abd-2026.git
   cd tugas1-abd-2026
   ```

2. Jika image `tugas1-bigdata` belum tersedia, bangun image dari folder proyek. Pada clone baru, lakukan ini sebelum mengunduh dataset agar file besar tidak ikut masuk ke konteks build.

   ```powershell
   docker build -t tugas1-bigdata .
   ```

3. Unduh `wikipedia_id-raw.csv` dan letakkan di `data/raw/` sesuai [petunjuk unduh](data/README.md#cara-memperoleh-data). Notebook 01 dan 02 sudah memakai nama dan lokasi file tersebut.

4. Buat dan jalankan container dari folder proyek. Folder itu dipasang ke `/home/jovyan/work`, sehingga notebook membaca data lokal dan hasil cleaning tersimpan kembali di folder proyek.

   ```powershell
   $repoTugas = (Get-Location).Path
   docker run -d --name tugas1-abd-jupyter -p 127.0.0.1:8888:8888 --mount "type=bind,source=$repoTugas,target=/home/jovyan/work" tugas1-bigdata
   ```

   Jika container tersebut sudah dibuat dan sedang berhenti, jalankan `docker start tugas1-abd-jupyter`. JupyterLab memakai akses lokal melalui `127.0.0.1`.

5. Buka [JupyterLab](http://127.0.0.1:8888/lab), masuk ke folder `notebooks/`, lalu jalankan **Run → Run All Cells** pada notebook 01, 02, dan 03 secara berurutan. Notebook 02 membuat `data/processed/wikipedia_id-bersih.parquet`, yang menjadi input Notebook 03. Buka Notebook 03 melalui JupyterLab untuk berinteraksi dengan grafik.

Untuk menghentikan JupyterLab, jalankan `docker stop tugas1-abd-jupyter`.

## Struktur repository

```text
.
├── .github/workflows/lint_check.yml
├── data/
│   ├── README.md
│   ├── raw/          # CSV yang diunduh
│   └── processed/    # Parquet yang dibuat Notebook 02
├── notebooks/
│   ├── 01_data_profiling.ipynb
│   ├── 02_data_cleaning.ipynb
│   └── 03_eda_and_insights.ipynb
├── src/
├── Dockerfile
├── requirements.txt
├── identitas.md
└── README.md
```

## Ketentuan tugas

Template berasal dari [repository tugas dosen](https://github.com/UMM-GURU/tugas1-abd-2026). Setelah repository GitHub Classroom dibuat, gunakan format nama `tugas1-[username_github]` sesuai instruksi template.

## Milestone

| Tahap | Target | Bukti yang dikumpulkan |
|---|---|---|
| Milestone 1 | Pertemuan 3 | Dataset >= 500 MB atau > 1 juta baris, `data/README.md`, dan `notebooks/01_data_profiling.ipynb` |
| Milestone 2 | Pertemuan 5 | Cleaning dengan Polars dan profiling DuckDB `SUMMARIZE` |
| Milestone 3 | Pertemuan 8 | Analisis temporal/ruang dan minimal 6 visualisasi interaktif |
| Final | Pertemuan 10 | Minimal 5 insight, Dockerfile, dan dokumentasi akhir |

## Output yang Diharapkan

Pada final submission, repository harus menghasilkan analisis yang dapat dijalankan ulang dari awal dan memuat:

- Dataset Indonesia yang terdokumentasi, berukuran minimal 500 MB atau lebih dari 1 juta baris, beserta instruksi unduhnya.
- Notebook profiling, cleaning, serta EDA yang dapat dieksekusi berurutan.
- Cleaning dan validasi data untuk missing values, duplikasi, outlier, serta aturan kualitas yang relevan dengan dataset.
- Analisis temporal atau spasial, minimal 6 visualisasi interaktif dengan Plotly atau Altair, dan minimal 5 insight analitik yang didukung hasil analisis.
- Environment yang dapat direproduksi melalui `Dockerfile`, `requirements.txt`, dan petunjuk eksekusi di README.
- Riwayat commit bertahap yang menunjukkan proses kerja pada setiap milestone.

## Kaitan dengan Materi Perkuliahan

| Materi | Pertemuan | Penerapan pada Tugas 1 |
|---|:---:|---|
| Polars dan lazy evaluation | 2 | Profiling dan transformasi dataset besar dengan `scan_*`, expressions, dan pipeline efisien. |
| DuckDB dan format data | 3 | Query analitik serta profiling dengan `SUMMARIZE`; gunakan Parquet bila sesuai. |
| Data quality dan cleaning | 4 | Tangani missing values, duplikasi, outlier, dan validasi data. |
| EDA dan visualisasi | 5 | Bangun visualisasi interaktif serta rumuskan insight analitik. |
| Time series | 6 | Terapkan analisis pola waktu atau pola spasial yang relevan dengan dataset. |
| Dask, NLP, dan ML | 7-9 | Opsional sebagai pengembangan jika relevan dengan skala dan pertanyaan analisis. |
| Streamlit dan Docker | 10-11 | Dokumentasikan environment Docker; dashboard Streamlit bersifat opsional untuk Tugas 1. |

## Ringkasan Penilaian

Nilai Tugas 1 berbobot 25% dari nilai akhir. Penilaian lengkap ada di dokumen spesifikasi tugas; ringkasannya sebagai berikut.

| Aspek | Bobot | Indikator utama |
|---|:---:|---|
| Konsistensi commit dan GitHub workflow | 20% | Commit bertahap, pesan deskriptif, struktur repository rapi, dan pemeriksaan otomatis lulus. |
| Data handling dengan Polars dan DuckDB | 25% | Pengolahan data besar efisien, memakai Polars dan DuckDB sesuai peran masing-masing. |
| Cleaning dan data quality | 15% | Profiling, validasi, serta penanganan null dan outlier terdokumentasi. |
| Visualisasi dan insight | 25% | Visualisasi interaktif informatif dan insight analitik yang didukung data. |
| Reproduktivitas dan Docker | 15% | Dockerfile, dependency, dan instruksi eksekusi memungkinkan proyek dijalankan ulang. |

Target kualitas tertinggi adalah analisis yang efisien, terdokumentasi, dapat direproduksi, dan memperlihatkan proses kerja konsisten sepanjang milestone; bukan hanya hasil akhir yang terlihat baik.

## Verifikasi pengumpulan

Periksa perubahan dengan `git status`, lalu buat commit bertahap dengan pesan deskriptif. Setelah menjalankan `git push origin main`, buka repository fork di GitHub untuk memastikan file tugas dan commit milestone tersedia. Bagikan URL repository jika diminta dosen.

## Aturan Teknis

- Gunakan Polars untuk manipulasi data dan DuckDB untuk analitik SQL.
- Dataset harus minimal 500 MB atau lebih dari 1 juta baris.
- Jangan commit file data besar; simpan instruksi unduhan dan sumber data pada `data/README.md`.
- Gunakan commit bertahap dan pesan yang jelas, misalnya `feat: add initial dataset profiling`.

## Integritas Akademik dan Penggunaan AI

- Dilarang menyalin kode, laporan, atau visualisasi mahasiswa lain maupun repository publik tanpa sitasi dan atribusi yang jelas.
- Dilarang menggunakan jasa joki atau menyerahkan pekerjaan yang tidak dapat dijelaskan sendiri.
- AI boleh digunakan untuk belajar, mencari rujukan, menjelaskan konsep, atau debugging. AI tidak menggantikan tanggung jawab mahasiswa atas kebenaran dan kualitas solusi.
- Mahasiswa wajib dapat menjelaskan setiap bagian kode, menjalankan serta memverifikasi ulang hasilnya, dan memastikan penggunaan Polars serta DuckDB sesuai standar kuliah.
- Setiap penggunaan AI harus dicantumkan pada bagian AI Disclosure Statement di bawah.
- Pelanggaran pertama bernilai 0 untuk tugas terkait; pelanggaran berikutnya dapat berakibat nilai E untuk mata kuliah sesuai ketentuan akademik.

## AI Disclosure Statement

Isi bagian ini sebelum pengumpulan akhir.

> Alat AI yang digunakan: [nama alat].
>
> Bagian yang dibantu: [contoh: penjelasan error Polars atau review dokumentasi].
>
> Verifikasi yang dilakukan: [contoh: menjalankan ulang kode, memeriksa dokumentasi resmi, dan memahami setiap cell].
