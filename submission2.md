# Laporan Proyek Machine Learning - Henry Dwi Prana Sitepu

## Project Overview

Dalam era digital saat ini, pengguna dihadapkan pada ribuan hingga jutaan pilihan buku yang tersedia secara online. Tantangan utama adalah bagaimana membantu pengguna menemukan buku yang paling sesuai dengan minat mereka tanpa harus memilah satu per satu. Fenomena ini dikenal sebagai information overload, yaitu kondisi ketika terlalu banyak informasi justru membuat proses pengambilan keputusan menjadi tidak efisien [1].

Salah satu solusi paling efektif untuk masalah ini adalah penggunaan sistem rekomendasi. Sistem ini telah banyak digunakan oleh berbagai platform besar seperti Amazon dan Goodreads untuk meningkatkan pengalaman pengguna sekaligus mendorong pertumbuhan penjualan. Menurut laporan McKinsey, lebih dari 35% penjualan Amazon dipengaruhi oleh sistem rekomendasi yang cerdas [2].

Dalam proyek ini, dibangun sebuah sistem rekomendasi buku berbasis hybrid, yang menggabungkan pendekatan content-based filtering (berbasis kemiripan judul buku menggunakan TF-IDF) dan collaborative filtering (berbasis kesamaan perilaku rating antar pengguna). Tujuan dari pendekatan hybrid ini adalah untuk mengatasi kelemahan masing-masing metode dan menghasilkan rekomendasi yang lebih akurat dan personal. Bobot kombinasi diatur secara seimbang (0.5 untuk masing-masing), dan sistem mengembalikan 10 rekomendasi terbaik untuk pengguna tertentu.

Model dievaluasi menggunakan metrik RMSE (Root Mean Squared Error), yang menunjukkan bahwa prediksi rating berada pada rata-rata selisih 3 poin dari rating aktual—suatu hasil yang cukup baik untuk sistem rekomendasi berbasis implicit feedback.

Referensi:

[1] T. H. Davenport & J. C. Beck, The Attention Economy: Understanding the New Currency of Business, Harvard Business Press, 2001.

[2] J. Manyika et al., "Big Data: The Next Frontier for Innovation, Competition, and Productivity," McKinsey Global Institute, 2011.

## Business Understanding

### Problem Statements

  - Information Overload: Pengguna kesulitan menemukan buku yang relevan dari ribuan pilihan buku yang tersedia di platform digital.

  - Keterbatasan Sistem Rekomendasi Tunggal: Sistem rekomendasi konvensional yang hanya menggunakan satu pendekatan (seperti content-based atau collaborative filtering saja) sering kali menghasilkan rekomendasi yang tidak cukup akurat atau terlalu sempit cakupannya.

### Goals
  - Meningkatkan Relevansi Rekomendasi Buku: Membangun sistem rekomendasi yang mampu memberikan saran buku yang relevan dan sesuai minat pengguna secara personal.
  - Meningkatkan Akurasi dengan Pendekatan Hybrid: Menggabungkan dua metode rekomendasi untuk mengatasi kelemahan masing-masing dan menghasilkan prediksi yang lebih baik.

### Solution Approach

Solution Statements:
Untuk mencapai tujuan tersebut, dua pendekatan digabungkan secara strategis:

  - Content-Based Filtering
    Menggunakan judul buku sebagai fitur utama, diproses melalui TF-IDF Vectorizer untuk merepresentasikan setiap buku sebagai vektor. Kemudian dihitung kemiripannya menggunakan cosine similarity. Ini memungkinkan sistem menyarankan buku yang memiliki kemiripan konten dengan buku yang sebelumnya disukai pengguna.
  - Collaborative Filtering (Item-Based)
    Menggunakan rating dari pengguna lain untuk menemukan kemiripan antar buku berdasarkan pola perilaku rating. Cocok untuk menemukan buku populer di kalangan pengguna dengan minat serupa.
  - Hybrid Model (Weighted)
    Kedua pendekatan digabungkan menggunakan weighted average dengan parameter α = 0.5 (kombinasi seimbang). Ini memungkinkan sistem untuk mempertimbangkan kemiripan konten sekaligus preferensi komunitas pengguna. Model ini memberikan 10 rekomendasi buku terbaik untuk setiap pengguna.

    ### Keterukuran Solusi
    Solusi yang diberikan pada proyek ini dapat dievaluasi secara kuantitatif menggunakan metrik **Root Mean Squared Error (RMSE)**. Metrik ini dipilih karena sesuai untuk mengukur seberapa akurat sistem dalam memprediksi
    rating buku yang relevan untuk pengguna. Nilai RMSE yang rendah menunjukkan bahwa sistem mampu memberikan rekomendasi yang mendekati preferensi aktual pengguna, sehingga dapat digunakan sebagai indikator keberhasilan
    dari pendekatan sistem rekomendasi yang dibangun.


## Data Understanding
Dataset yang digunakan berasal dari **Book-Crossing Dataset** oleh GroupLens Research.

**Sumber Dataset:** https://www.kaggle.com/code/fahadmehfoooz/book-recommendation-system

Dataset ini terdiri dari tiga file utama:
- **Books.csv**: berisi informasi tentang buku.
- **Ratings.csv**: berisi data rating yang diberikan pengguna terhadap buku.
- **Users.csv**: berisi informasi tentang pengguna (tidak digunakan dalam model).

### Informasi Data:

| File          | Jumlah Baris | Jumlah Kolom | Keterangan                                            |
|---------------|---------------|---------------|--------------------------------------------------------|
| Books.csv     | 271,360       | 8             | Berisi metadata buku                                   |
| Ratings.csv   | 1,149,780     | 3             | Berisi interaksi rating pengguna terhadap buku         |
| Users.csv     | 278,858       | 3             | Berisi informasi lokasi dan usia pengguna              |

Setelah preprocessing, hanya digunakan:
- Pengguna dengan lebih dari 10 interaksi
- Buku dengan lebih dari 50 interaksi


### Variabel pada Dataset:

#### Books.csv
- `ISBN`: Nomor unik identifikasi buku (International Standard Book Number).
- `Book-Title`: Judul buku.
- `Book-Author`: Nama penulis buku.
- `Year-Of-Publication`: Tahun terbit.
- `Publisher`: Penerbit buku.
- `Image-URL-S`, `Image-URL-M`, `Image-URL-L`: URL gambar buku dalam berbagai ukuran.

#### Ratings.csv
- `User-ID`: ID pengguna.
- `ISBN`: ID buku.
- `Book-Rating`: Nilai rating (0–10), di mana 0 menunjukkan tidak ada rating eksplisit.

#### Users.csv
- `User-ID`: ID pengguna.
- `Location`: Kota, Provinsi, Negara.
- `Age`: Usia pengguna.

### Visualisasi dan Eksplorasi Data
Distribusi rating buku dianalisis untuk memahami persebaran skor yang diberikan pengguna.

![EDA_Visualisasi](https://github.com/user-attachments/assets/2a5dd3a0-09b1-408b-8b15-cbd72f4e355c)

**Insight:**
- Rating 0 mendominasi data, mengindikasikan banyak pengguna tidak memberikan rating eksplisit.
- Rating eksplisit berkisar dari 1 hingga 10, dengan puncak antara 7–9.
- Pola ini mendukung keputusan untuk memfilter pengguna dan buku berdasarkan aktivitas agar sistem rekomendasi lebih akurat.

## Data Preparation
Pada tahap ini, dilakukan beberapa langkah penting untuk menyiapkan data sebelum digunakan dalam model rekomendasi. Setiap teknik yang digunakan memiliki tujuan untuk meningkatkan kualitas data dan akurasi sistem rekomendasi.

### 1. Penyaringan Pengguna dan Buku
- **Tujuan:** Menghindari cold start dan sparsity (kekosongan data) dengan hanya menyertakan pengguna dan buku yang memiliki jumlah interaksi memadai.

### 2. Merge Metadata Buku
- **Tujuan:** Menggabungkan informasi judul buku agar dapat digunakan pada content-based filtering.

### 3. Representasi Vektor TF-IDF
- **Tujuan:** Mengubah teks judul buku menjadi vektor numerik yang dapat dihitung kemiripannya.

### 4. Cosine Similarity untuk Content-Based
- **Tujuan:** Menghitung kemiripan antar buku berdasarkan representasi TF-IDF.

### 5. Pivot Table untuk User-Item Matrix
- **Tujuan:** Membuat matriks interaksi pengguna–item yang digunakan untuk collaborative filtering.

### 6. Normalisasi dan Similarity untuk Collaborative Filtering
- **Tujuan:** Mengisi nilai kosong dengan 0 dan menghitung kesamaan antar item berdasarkan rating pengguna.

Tahapan ini penting untuk memastikan bahwa model bekerja hanya dengan data yang relevan dan berkualitas tinggi.


## Modeling
Pada tahap ini, dibangun sistem rekomendasi untuk menghasilkan Top-10 Recommendation dengan dua pendekatan yang berbeda, yaitu:

### 1. Content-Based Filtering
- Menggunakan TF-IDF untuk menghitung kemiripan antar buku berdasarkan judul.
- Kemiripan dihitung dengan cosine similarity antar TF-IDF vektor.


### 2. Collaborative Filtering (Item-Based)
- Menggunakan user-item rating matrix.
- Menghitung cosine similarity antar item berdasarkan pola rating pengguna.


### 3. Hybrid Recommendation
- Menggabungkan skor dari content-based dan collaborative filtering.
- Formula gabungan: `combined_scores = alpha * content_score + (1 - alpha) * collaborative_score`
  alpha = 0.5
- Output: Top-10 recommendation yang lebih akurat dan relevan.

## Evaluation
Metrik ini digunakan karena sistem rekomendasi berbasis rating perlu mengukur seberapa akurat prediksi sistem terhadap rating aktual yang diberikan oleh pengguna. RMSE cocok digunakan karena memberikan penalti 
yang lebih besar pada prediksi yang jauh dari nilai sebenarnya, sehingga model dapat dioptimalkan untuk mengurangi kesalahan besar.

### Hasil Evaluasi:
- RMSE yang dihasilkan: 3.61

Nilai RMSE sebesar 3.61 mengindikasikan bahwa secara rata-rata, prediksi rating dari sistem menyimpang sekitar 3.61 poin dari rating aktual pengguna pada skala 0–10.

Meskipun nilai ini masih menunjukkan adanya error yang cukup besar, hasil ini sudah mencerminkan kecenderungan sistem dalam mempelajari pola rating pengguna.

Grafik distribusi error menunjukkan bahwa sebagian besar prediksi cukup dekat dengan nilai aktual, walau terdapat ekor kanan (right skew) yang menandakan adanya overestimasi pada sebagian prediksi.

