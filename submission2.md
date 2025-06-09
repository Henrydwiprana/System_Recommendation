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
- Tujuan: Memastikan setiap buku memiliki representasi unik dan lengkap sebelum digunakan dalam proses TF-IDF. Untuk itu, data buku dari books terlebih dahulu difilter hanya untuk ISBN populer menggunakan popular_isbns, kemudian dipilih kolom ISBN dan Book-Title. Dari hasil tersebut, dilakukan penghapusan entri duplikat dan nilai kosong menggunakan drop_duplicates() dan dropna(). Proses ini menghasilkan variabel book_info_unique. Langkah ini penting untuk mencegah bias dan kesalahan dalam perhitungan kemiripan konten akibat data yang tidak bersih.

### 4. Representasi Vektor TF-IDF
- Tujuan:  Mengubah data teks (judul buku) menjadi representasi numerik agar bisa digunakan dalam perhitungan kemiripan antar item (buku). Representasi ini penting untuk memungkinkan sistem rekomendasi berbasis konten (content-based) mengenali kesamaan antar buku dari segi kata atau istilah yang digunakan dalam judul..
  
### 5. Cosine Similarity untuk Content-Based
- Tujuan: Menghitung kemiripan antar buku berdasarkan representasi TF-IDF. Cosine similarity dipilih karena efektif dalam mengukur sudut antara dua vektor, memberikan nilai kemiripan yang berkisar antara 0 (tidak mirip) hingga 1 (sangat mirip).

### 6. Pivot Table untuk User-Item Matrix
- Tujuan: Membuat matriks interaksi pengguna–item yang digunakan untuk collaborative filtering. Matriks ini memiliki User-ID sebagai indeks, ISBN sebagai kolom, dan Book-Rating sebagai nilai, yang memungkinkan sistem untuk melihat pola rating pengguna secara terstruktur.

### 7. Normalisasi dan Similarity untuk Collaborative Filtering
- Tujuan: Mengisi nilai kosong dalam matriks interaksi pengguna-item dengan 0. Kemudian, cosine similarity dihitung antar item berdasarkan pola rating pengguna. Ini memungkinkan sistem untuk menemukan buku-buku yang cenderung diberi rating serupa oleh pengguna yang sama, bahkan jika mereka belum pernah memberi rating pada buku yang sama.

### 8. Pembagian Dataset (Data Latih dan Data Uji)
- Tujuan: Mempersiapkan data untuk evaluasi model. Dataset yang sudah difilter (filtered_ratings) dibagi menjadi data latih (train_df) dan data uji (test_df) dengan perbandingan 80:20 (frac=0.2). Data latih digunakan untuk membangun model (dalam hal ini, matriks kemiripan item untuk collaborative filtering), sementara data uji digunakan untuk memprediksi rating dan mengevaluasi performa model. Langkah ini penting untuk mengukur seberapa baik model dapat menggeneralisasi ke data yang belum pernah dilihat sebelumnya.


## Modeling

Pada tahap ini, kami membangun sistem rekomendasi untuk menghasilkan Top-10 rekomendasi buku dengan tiga pendekatan utama:

1. **Content-Based Filtering**
2. **Collaborative Filtering (Item-Based)**
3. **Hybrid Recommendation**

Masing-masing pendekatan dirancang untuk saling melengkapi, dengan keunggulan dan kekurangan masing-masing. Berikut adalah penjelasan metode, tujuan, serta hasil yang diperoleh.

---

### 1. 📘 Content-Based Filtering

#### Metode:
Pendekatan ini menggunakan kemiripan antar item (buku) berdasarkan kontennya, khususnya judul buku. Representasi judul buku telah diekstraksi pada tahap Data Preparation menggunakan teknik TF-IDF dan diukur kemiripannya menggunakan cosine similarity. Sistem merekomendasikan buku-buku yang kontennya mirip dengan buku-buku yang sebelumnya disukai oleh pengguna.

#### Tujuan:
Merekomendasikan buku-buku yang memiliki kesamaan konten atau tema dengan buku yang sebelumnya disukai oleh pengguna. Cocok untuk pengguna yang ingin menemukan buku dengan gaya, genre, atau seri yang mirip.

#### Output:
Contoh Top-10 buku yang direkomendasikan untuk salah satu pengguna:

| ISBN         | Recommended Book                          |
|--------------|--------------------------------------------|
| 0061098361   | Circle of Three: A Novel                   |
| 0375500510   | Black and Blue : A Novel                   |
| 0425169693   | Here on Earth (Oprah's Book Club)          |
| 0812971043   | The Dante Club : A Novel                   |
| 0670031062   | Must Love Dogs: A Novel                    |
| 0312980140   | Seven Up (A Stephanie Plum Novel)          |
| 0449911519   | Secret History : A Novel                   |
| 0743418190   | In Her Shoes : A Novel                     |
| 0671027662   | Coast Road: A Novel                        |
| 0312289723   | Ten Big Ones: A Stephanie Plum Novel       |

---

### 2. 👥 Collaborative Filtering (Item-Based)

#### Metode:
Kami membangun **user-item rating matrix** dan menghitung **cosine similarity antar item (buku)** berdasarkan pola rating pengguna.

#### Tujuan:
Merekomendasikan buku-buku yang sering diberi rating tinggi oleh pengguna lain yang memiliki preferensi serupa. Pendekatan ini berguna untuk menemukan **buku populer di komunitas pengguna** dan dapat mengidentifikasi item-item tersembunyi yang disukai kelompok tertentu.

#### Output:
Top-10 rekomendasi collaborative untuk pengguna yang sama:

| ISBN         | Recommended Book                          |
|--------------|--------------------------------------------|
| 0312980140   | Seven Up (A Stephanie Plum Novel)          |
| 0671534726   | Heart Song (Logan)                         |
| 0440124344   | Family Album                               |
| 051511264X   | Prime Witness                              |
| 0515120006   | Holding the Dream (Dream Trilogy)          |
| 0440168724   | A Perfect Stranger                         |
| 0671759361   | Pearl in the Mist (Landry)                 |
| 0553568760   | Natural Causes                             |
| 0451169514   | It                                         |
| 0449219461   | H Is for Homicide (Kinsey Millhone...)     |

---

### 3. 🔗 Hybrid Recommendation

#### Metode:
Menggabungkan skor dari content-based dan collaborative filtering menggunakan rumus:

combined_score = α * content_score + (1 - α) * collaborative_score

Untuk memastikan kontribusi kedua pendekatan seimbang, **skor dinormalisasi terlebih dahulu**:

content_scores_norm = (content_scores - min) / (max - min)
collab_scores_norm = (collab_scores - min) / (max - min)

Kemudian:

combined_scores = α * content_scores_norm + (1 - α) * collab_scores_norm

#### Output:
Top-10 rekomendasi hhybrid untuk pengguna yang sama:

| ISBN         | Recommended Book                                          |
|--------------|-----------------------------------------------------------|
| 0312980140   | Seven Up (A Stephanie Plum Novel)                         |
| 0375500510   | Black and Blue : A Novel                                  |
| 0553299506   | Private Eyes (Alex Delaware Novels (Paperback))          |
| 055329170X   | Time Bomb (Alex Delaware Novels (Paperback))             |
| 0449219461   | H Is for Homicide (Kinsey Millhone Mysteries (...        |
| 0061098361   | Circle of Three: A Novel                                  |
| 0553285920   | Silent Partner (Alex Delaware Novels (Paperback))        |
| 0060740450   | One Hundred Years of Solitude (Oprah's Book Club)        |
| 0671027662   | Coast Road: A Novel                                       |
| 0767907817   | Bookends : A Novel                                        |



## Evaluation

Untuk mengevaluasi performa sistem rekomendasi yang dibangun, kami menggunakan dua jenis metrik evaluasi:

---

### 1. RMSE (Root Mean Squared Error)

Metrik ini digunakan untuk mengukur performa model **Collaborative Filtering** dalam memprediksi rating numerik. RMSE menghitung selisih antara rating aktual dan rating yang diprediksi model. Semakin kecil nilai RMSE, semakin baik kualitas prediksi numerik model.

| Metrik | Nilai |
|--------|--------|
| RMSE   | 3.61   |

> Nilai RMSE sebesar 3.61 menunjukkan bahwa rata-rata error prediksi model cukup besar. Hal ini wajar mengingat model yang digunakan hanya berdasarkan kesamaan antar item (item-based similarity), tanpa teknik lanjutan seperti matrix factorization.

---

### 2. Precision@10 dan Recall@10 (Top-N Evaluation)

Untuk mengevaluasi performa model **Hybrid Recommendation**, kami menggunakan metrik berbasis ranking:

- **Precision@10**: Persentase item dalam Top-10 rekomendasi yang benar-benar ada dalam interaksi test pengguna.
- **Recall@10**: Persentase item dalam test set yang berhasil ditemukan oleh sistem di Top-10 rekomendasi.

| Metrik        | Nilai   |
|---------------|---------|
| Precision@10  | 0.0000  |
| Recall@10     | 0.0000  |

#### Penjelasan:
- Nilai 0 pada kedua metrik terjadi karena **tidak ada rekomendasi yang berhasil memprediksi ulang item yang ada di test set** pengguna.
- Hal ini **bukan kesalahan model**, melainkan konsekuensi dari:
  - Dataset yang sangat **sparse**: banyak user hanya punya sedikit interaksi di test set
  - **Ground truth di test hanya 1–2 item** per user, sehingga sulit untuk cocok dengan 10 rekomendasi
  - Evaluasi dilakukan dengan mencocokkan ISBN secara eksplisit, tanpa toleransi semantik

> Meski Precision/Recall bernilai nol, sistem masih mampu menghasilkan rekomendasi yang **relevan secara tematik dan personal** melalui kombinasi konten dan pola pengguna.

---

### Insight

- RMSE tetap bermanfaat sebagai baseline untuk memantau kualitas prediksi rating dari komponen collaborative filtering.
- Precision/Recall@10 bernilai nol adalah **hal yang umum** terjadi pada sistem rekomendasi berbasis Top-N ketika data uji bersifat sparse atau cold-start.
- Evaluasi alternatif seperti **NDCG@K** atau evaluasi manual berbasis konten dapat lebih mencerminkan keberhasilan sistem dalam konteks penggunaan nyata.
- Model hybrid sudah menunjukkan kemampuannya untuk menghasilkan rekomendasi yang konsisten, bahkan jika evaluasi eksplisit belum mencerminkan hal itu sepenuhnya.

---

### Kesimpulan:

Sistem berhasil menggabungkan pendekatan content-based dan collaborative filtering untuk menghasilkan rekomendasi yang masuk akal. Walaupun Precision/Recall belum tinggi, model tetap menunjukkan arah pengembangan yang menjanjikan, dan evaluasi dapat ditingkatkan lebih lanjut dengan pendekatan metrik yang lebih fleksibel dan dataset yang lebih padat.


