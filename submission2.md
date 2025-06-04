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

**Sumber Dataset:** https://www.kaggle.com/datasets/arashnic/book-recommendation-dataset

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
- Tujuan: Menghindari cold start (pengguna atau buku baru tanpa riwayat interaksi yang cukup) dan sparsity (kekosongan data) dengan hanya menyertakan pengguna dan buku yang memiliki jumlah interaksi memadai. Dalam proyek ini, hanya pengguna dengan lebih dari 10 interaksi dan buku dengan lebih dari 50 interaksi yang dipertahankan. Ini membantu memastikan bahwa model dilatih pada data yang memiliki pola perilaku yang cukup untuk dipelajari.
  
### 2. Merge Metadata Buku
- Tujuan: Menggabungkan informasi judul buku dari Books.csv ke dalam dataset Ratings.csv yang sudah difilter (filtered_ratings). Penggabungan ini penting agar informasi judul buku dapat diakses dan digunakan untuk content-based filtering.
  

### 3. Pembersihan Data Duplikat dan Nilai Kosong pada Metadata Buku
- Tujuan: Memastikan setiap buku memiliki representasi unik dan lengkap untuk proses TF-IDF. Sebelum membuat matriks TF-IDF, dilakukan penghapusan entri duplikat pada kombinasi ISBN dan judul buku, serta menghilangkan baris yang memiliki nilai kosong pada kolom judul. Ini dilakukan pada book_info_unique = filtered_ratings[['ISBN', 'Book-Title']].drop_duplicates().dropna(). Langkah ini krusial untuk mencegah bias dan kesalahan dalam perhitungan kemiripan konten akibat data yang tidak bersih.

### 4. Representasi Vektor TF-IDF
- Tujuan: Mengubah teks judul buku menjadi vektor numerik yang dapat dihitung kemiripannya. Teknik TF-IDF Vectorizer digunakan untuk merepresentasikan setiap judul buku sebagai vektor, di mana setiap nilai dalam vektor mencerminkan seberapa penting sebuah kata dalam judul buku relatif terhadap seluruh koleksi judul buku. Stop words dalam bahasa Inggris juga dihilangkan untuk meningkatkan relevansi representasi.
  
### 5. Cosine Similarity untuk Content-Based
- Tujuan: Menghitung kemiripan antar buku berdasarkan representasi TF-IDF. Cosine similarity dipilih karena efektif dalam mengukur sudut antara dua vektor, memberikan nilai kemiripan yang berkisar antara 0 (tidak mirip) hingga 1 (sangat mirip).

### 6. Pivot Table untuk User-Item Matrix
- Tujuan: Membuat matriks interaksi pengguna–item yang digunakan untuk collaborative filtering. Matriks ini memiliki User-ID sebagai indeks, ISBN sebagai kolom, dan Book-Rating sebagai nilai, yang memungkinkan sistem untuk melihat pola rating pengguna secara terstruktur.

### 7. Normalisasi dan Similarity untuk Collaborative Filtering
- Tujuan: Mengisi nilai kosong dalam matriks interaksi pengguna-item dengan 0. Kemudian, cosine similarity dihitung antar item berdasarkan pola rating pengguna. Ini memungkinkan sistem untuk menemukan buku-buku yang cenderung diberi rating serupa oleh pengguna yang sama, bahkan jika mereka belum pernah memberi rating pada buku yang sama.

### 8. Pembagian Dataset (Data Latih dan Data Uji)
- Tujuan: Mempersiapkan data untuk evaluasi model. Dataset yang sudah difilter (filtered_ratings) dibagi menjadi data latih (train_df) dan data uji (test_df) dengan perbandingan 80:20 (frac=0.2). Data latih digunakan untuk membangun model (dalam hal ini, matriks kemiripan item untuk collaborative filtering), sementara data uji digunakan untuk memprediksi rating dan mengevaluasi performa model. Langkah ini penting untuk mengukur seberapa baik model dapat menggeneralisasi ke data yang belum pernah dilihat sebelumnya.

Tahapan ini penting untuk memastikan bahwa model bekerja hanya dengan data yang relevan dan berkualitas tinggi.


## Modeling
Pada tahap ini, kami membangun sistem rekomendasi untuk menghasilkan Top-10 Rekomendasi dengan dua pendekatan utama: Content-Based Filtering dan Collaborative Filtering, yang kemudian kami gabungkan dalam model Hybrid.

### 1. Content-Based Filtering
Pendekatan ini berfokus pada kemiripan atribut antar item. Kami menggunakan TF-IDF (Term Frequency-Inverse Document Frequency) untuk merepresentasikan judul buku sebagai vektor numerik. Kemudian, cosine similarity digunakan untuk menghitung kemiripan antar vektor judul buku tersebut.

- Tujuan :
  Merekomendasikan buku-buku yang memiliki kesamaan konten atau tema dengan buku-buku yang sebelumnya disukai atau diberi rating tinggi oleh pengguna. Ini efektif untuk mencari rekomendasi yang relevan secara topikal.

 Output: Top-10 recommendation
 
### 2. Collaborative Filtering (Item-Based)
Pendekatan ini berfokus pada kemiripan perilaku rating antar item. Kami membangun user-item rating matrix dan kemudian menghitung cosine similarity antar item berdasarkan pola rating pengguna.

- Tujuan:
  Merekomendasikan buku-buku yang sering diberi rating tinggi oleh pengguna lain yang memiliki selera serupa, atau buku yang sering dibaca/diberi rating bersamaan dengan buku yang disukai pengguna. Ini sangat berguna untuk menemukan "permata tersembunyi" atau buku populer dalam komunitas selera pengguna.

 Output: Top-10 recommendation
 
### 3. Hybrid Recommendation
Pendekatan ini menggabungkan skor dari content-based dan collaborative filtering menggunakan formula bobot:
- Formula gabungan: `combined_scores = alpha * content_score + (1 - alpha) * collaborative_score`
  alpha = 0.5
- Tujuan:
  Mengatasi kelemahan masing-masing metode tunggal (misalnya, Content-Based yang mungkin terlalu sempit atau Collaborative Filtering yang rentan cold start dan sparsity) untuk menghasilkan rekomendasi yang lebih akurat, personal, dan robust. Model hybrid ini dirancang untuk memberikan pengalaman rekomendasi yang optimal dengan memanfaatkan kekuatan kedua pendekatan.

  Output: Top-10 recommendation .

## Evaluation
Dalam proyek ini, digunakan dua jenis metrik evaluasi yang sesuai dengan karakteristik pendekatan sistem rekomendasi yang dibangun:

1. Root Mean Squared Error (RMSE)
   Digunakan untuk mengevaluasi performa model Collaborative Filtering, yang melakukan prediksi rating eksplisit dari pengguna terhadap buku.

2. Precision@10 dan Recall@10
   Digunakan untuk mengevaluasi performa model Hybrid, yang menghasilkan daftar rekomendasi Top-10 buku untuk setiap pengguna. Metrik ini lebih sesuai karena fokus model hybrid adalah memberikan rekomendasi, bukan memprediksi nilai rating.

### Hasil Evaluasi:
- RMSE yang dihasilkan: 3.61

Nilai RMSE sebesar 3.61 mengindikasikan bahwa secara rata-rata, prediksi rating dari sistem menyimpang sekitar 3.61 poin dari rating aktual pengguna pada skala 0–10.

Meskipun nilai ini masih menunjukkan adanya error yang cukup besar, hasil ini sudah mencerminkan kecenderungan sistem dalam mempelajari pola rating pengguna.

Grafik distribusi error menunjukkan bahwa sebagian besar prediksi cukup dekat dengan nilai aktual, walau terdapat ekor kanan (right skew) yang menandakan adanya overestimasi pada sebagian prediksi.

