# Klasifikasi Kategori Berita Berbahasa Indonesia

Proyek UTS (Milestone 1) Machine Learning, Program Studi Informatika, Universitas Mikroskil, Semester Ganjil TA. 2026–2027.

## Identitas Proyek

| Informasi | Keterangan |
|---|---|
| **Project ID** | A13 |
| **Nama kelompok** | Project Berita |
| **Topik** | Berita |
| **Jenis tugas ML** | Klasifikasi teks multi-kelas (text mining) |

### Anggota Kelompok

| Nama | NIM |
|---|---|
| Matthew Rivandy | 231111820 |
| Oktapianus Zalukhu | 231111267 |
| Afni Titus Simanjuntak | 231111841 |

## Deskripsi Masalah

Portal berita online menerbitkan ribuan artikel setiap hari. Pengelompokan artikel ke kategori atau kanal secara manual memakan waktu, rawan tidak konsisten antar-editor, dan sulit diskalakan. Proyek ini membangun model yang memprediksi **kategori berita hanya dari judulnya**.

- **Unit observasi:** satu judul berita detik.com
- **Target:** `category`, 9 kelas (finance, food, health, hot, inet, news, oto, sport, travel)
- **Pendekatan:** classical machine learning, yaitu TF-IDF (unigram + bigram) dengan model linear
- **Metrik utama:** macro-F1 (kelas sangat tidak seimbang), dengan accuracy sebagai pelengkap

**Rumusan masalah**
1. Seberapa akurat kategori berita dapat diprediksi hanya dari judulnya?
2. Apakah LinearSVC memberikan peningkatan yang berarti dibandingkan baseline regresi logistik?
3. Kata atau frasa apa yang paling menentukan kategori, dan kapan model paling sering salah?

## Isi Repository

| File | Keterangan |
|---|---|
| `README.md` | Dokumen ini |
| `Project_Berita.ipynb` | Notebook eksperimen (data understanding, EDA, preprocessing, feature engineering, baseline, model kandidat, evaluasi) |
| `Laporan_UTS_Project_Berita.docx` | Laporan UTS lengkap (bagian A sampai I) |

## Dokumentasi Sumber Dataset

| Informasi | Keterangan |
|---|---|
| Nama dataset | Indonesian News Title Dataset |
| Sumber | detik.com, dikumpulkan oleh ibamibrahim (GitHub) |
| Tautan | https://github.com/ibamibrahim/dataset-judul-berita-indonesia |
| File | `detik_news_title.csv` |
| Ukuran mentah | 91.017 baris × 4 kolom (`date`, `url`, `title`, `category`) |
| Setelah pembersihan | 90.891 judul (duplikat dan baris kosong dibuang) |
| Periode | 1 Januari – 13 Juni 2020 |
| Cara memperoleh | Dimuat langsung dari URL raw GitHub di dalam notebook, tanpa upload manual |

Catatan penting tentang data:
- Distribusi kelas sangat timpang (news 35,6% vs sport 2,7%, rasio sekitar 13,3 : 1).
- Kolom `url` **tidak dipakai** sebagai fitur karena membocorkan label (subdomain sama dengan kategori).
- Ada 29 judul unik dengan label konflik; ditangani dengan mengambil kemunculan pertama.
- Data didominasi tema COVID-19 (puncak sekitar 39,9% pada Maret 2020), sehingga generalisasi ke periode lain belum teruji.

## Hasil Eksperimen Awal

| Model | Macro-F1 (test) | Accuracy (test) |
|---|---|---|
| Baseline: Logistic Regression (balanced) | 0,7597 | 0,7961 |
| **Model final: LinearSVC (C=0,3), TF-IDF 5.000 fitur** | **0,7885** | **0,8280** |

- LinearSVC unggul konsisten: menang pada 5 dari 5 fold CV, dengan selisih macro-F1 +0,0252 (CV) dan +0,0288 (test). Interval bootstrap 95% [+0,0233; +0,0351] tidak mencakup nol.
- Peningkatan terbesar datang dari kosakata TF-IDF yang lebih besar (20.000 fitur memberi macro-F1 CV 0,8278), bukan dari fitur numerik atau reduksi dimensi.
- Kesalahan terkonsentrasi pada kelas health, sport, travel, dan inet serta pasangan finance–news, sebagian besar karena label kanal yang tumpang tindih.

## Petunjuk Penggunaan Repository

1. Buka `Project_Berita.ipynb` di [Google Colab](https://colab.research.google.com/). Pilih **File → Upload notebook**, atau buka langsung lewat tab GitHub.
2. Pilih **Runtime → Run all**. Dataset dimuat otomatis dari URL, jadi tidak perlu mengunggah file apa pun.
3. Seluruh pembagian data dan model memakai `random_state=42`, sehingga hasil dapat direproduksi.
4. Estimasi waktu eksekusi sekitar 20–30 menit (ada beberapa cross-validation dan t-SNE).
5. Berkas hasil (`hasil_*.csv`, `TABEL_PERBANDINGAN_MODEL.csv`, `perbandingan_per_kelas.csv`, `hasil_fitur_tambahan.csv`) dihasilkan di folder kerja Colab setelah notebook dijalankan.
6. Detail metode, analisis, dan rencana lanjutan ada di `Laporan_UTS_Project_Berita.docx`.

## Teknologi

Python, pandas, NumPy, scikit-learn (TF-IDF, LinearSVC, Logistic Regression), matplotlib, seaborn, Google Colab.

## Rencana Lanjutan

- Error analysis lebih dalam untuk kelas health, sport, travel, dan inet.
- Audit label konflik dan label kanal yang tumpang tindih dengan news.
- Split temporal (latih Januari–April, uji Mei–Juni) untuk menguji generalisasi.
- Memperbesar kosakata TF-IDF dan memperluas grid hiperparameter, serta evaluasi dengan beberapa seed.
