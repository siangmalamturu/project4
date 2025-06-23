# Laporan Proyek Machine Learning Sistem Rekomendasi Film - Fathurrahman Azhari

## Project Overview

Dalam era digital yang dibanjiri oleh konten, pengguna seringkali kesulitan menemukan film yang sesuai dengan selera mereka di antara ribuan pilihan yang tersedia. Fenomena ini, yang dikenal sebagai *information overload* atau kelebihan informasi, menjadi tantangan utama bagi platform penyedia konten seperti Netflix, Amazon Prime, dan lainnya. Sistem rekomendasi hadir sebagai solusi untuk mengatasi masalah ini dengan cara menyaring dan menyajikan konten yang paling relevan bagi setiap pengguna secara personal. Dengan menganalisis preferensi pengguna dan atribut film, sistem ini tidak hanya membantu pengguna menemukan konten baru yang mereka sukai, tetapi juga secara signifikan meningkatkan *engagement* dan loyalitas pengguna terhadap platform.

### Mengapa dan Bagaimana Masalah Harus Diselesaikan:

Proyek ini akan membangun dua jenis sistem rekomendasi:

1.  **Content-Based Filtering**: Menganalisis atribut film untuk menemukan film serupa.
2.  **Collaborative Filtering**: Menganalisis pola rating dari komunitas pengguna untuk menemukan pengguna dengan selera serupa.

Dengan membandingkan kedua pendekatan ini, kita dapat memahami metode mana yang lebih efektif untuk dataset yang digunakan.

### Kebaruan Proyek ini:

Proyek ini menghadirkan kebaruan dengan memodifikasi fokus analisis pada kedua jenis sistem rekomendasi:
* Untuk **Content-Based Filtering**, analisis bergeser dari hanya genre ke **kata kunci yang diekstraksi dari judul film (TF-IDF pada Judul)**, yang berpotensi menangkap nuansa tematik yang lebih halus.
* Untuk **Collaborative Filtering**, alih-alih memprediksi rating eksplisit, kita berfokus pada **Popularitas Dinamis berbasis waktu**, untuk merekomendasikan film yang sedang tren atau aktif diinteraksikan oleh komunitas dalam periode waktu tertentu.

### Hasil Riset Terkait atau Referensi:

Banyak penelitian telah membuktikan efektivitas sistem rekomendasi. Menurut riset oleh J. S. Breese, D. Heckerman, dan C. Kadie, pendekatan *collaborative filtering* telah terbukti menjadi salah satu metode yang paling berhasil dalam memprediksi preferensi pengguna dengan menganalisis pola rating dari komunitas pengguna yang lebih besar. Riset lain oleh Isinkaye, Folajimi, dan Ojokoh (2015) juga menyoroti berbagai teknik, termasuk *content-based* dan *collaborative*, sebagai fondasi utama dalam sistem rekomendasi modern. Proyek ini akan mengimplementasikan kedua pendekatan tersebut untuk memberikan perbandingan yang komprehensif.

Referensi:
> [1] J. S. Breese, D. Heckerman, and C. Kadie, "Empirical analysis of predictive algorithms for collaborative filtering," *Proceedings of the Fourteenth conference on Uncertainty in artificial intelligence*, 1998, pp. 43–52.
> [2] F. O. Isinkaye, Y. O. Folajimi, and B. A. Ojokoh, "Recommendation systems: Principles, methods and evaluation," Egyptian Informatics Journal, vol. 16, no. 3, pp. 261-273, 2015.

## Business Understanding

### Problem Statements

Menjelaskan pernyataan masalah:
- Bagaimana cara membangun sebuah sistem yang dapat memberikan rekomendasi film yang dipersonalisasi berdasarkan **kata kunci judul film** yang pernah ditonton dan disukai oleh pengguna?
- Bagaimana cara membangun sebuah sistem yang dapat merekomendasikan film berdasarkan **popularitas dinamis dan tren rating terbaru dari pengguna lain**, tanpa memerlukan informasi tentang genre atau atribut film lainnya secara langsung?
- Bagaimana cara mengukur dan membandingkan performa dari kedua pendekatan sistem rekomendasi tersebut untuk mengetahui tingkat akurasi dan relevansinya dalam konteks kebaruan proyek ini?

### Goals

- Mengembangkan model rekomendasi **Content-Based Filtering yang mampu menyarankan film berdasarkan kesamaan kata kunci judul film**.
- Mengembangkan model rekomendasi **Collaborative Filtering menggunakan teknik popularitas dinamis berbasis waktu** untuk menemukan film baru berdasarkan tren selera komunitas.
- Mengevaluasi kedua model menggunakan metrik yang sesuai (Precision & Recall untuk Content-Based, dan analisis kualitatif/karakteristik untuk Popularitas Dinamis) untuk memahami kekuatan dan kelemahan masing-masing model dalam konteks kebaruan yang diusung.

### Solution statements

Untuk mencapai tujuan tersebut, dua pendekatan solusi akan diimplementasikan:

1.  **Content-Based Filtering**: Pendekatan ini akan merekomendasikan film dengan menganalisis kemiripan atribut film, khususnya pada **fitur kata kunci dari judul film**. Teks judul akan diubah menjadi representasi numerik menggunakan TF-IDF, dan kemiripan antar film akan dihitung menggunakan Cosine Similarity. Solusi ini menjawab masalah rekomendasi berbasis atribut.

2.  **Collaborative Filtering**: Pendekatan ini akan merekomendasikan film dengan mengidentifikasi **pola popularitas dan tren terbaru dalam data rating pengguna berdasarkan waktu**. Film dengan interaksi rating positif yang tinggi dalam periode waktu tertentu akan diidentifikasi dan direkomendasikan. Solusi ini menjawab masalah rekomendasi berbasis tren komunitas dan cocok untuk *cold-start problem* bagi pengguna baru.

## Data Understanding

Dataset yang digunakan dalam proyek ini adalah MovieLens 10M Dataset, yang berisi 10 juta rating film dari sekitar 72.000 pengguna untuk 10.000 film. Dataset ini merupakan sumber daya yang populer untuk penelitian dan pengembangan sistem rekomendasi karena ukurannya yang besar dan data rating yang otentik, yang memungkinkan pemodelan preferensi pengguna yang kompleks.

Dataset ini dapat diunduh melalui Kaggle: [MovieLens 10M Dataset](https://www.kaggle.com/datasets/amirmotefaker/movielens-10m-dataset-latest-version).

Data terdiri dari dua file utama: `movies.dat` dan `ratings.dat`.

Variabel pada dataset `movies.dat`:

-   `movie_id`: ID unik untuk setiap film. (Tipe: numerik)
-   `title`: Judul film beserta tahun rilisnya. (Tipe: teks)
-   `genres`: Satu atau lebih genre yang diasosiasikan dengan film, dipisahkan oleh karakter `|`. (Tipe: teks)

Variabel pada dataset `ratings.dat`:

-   `user_id`: ID unik untuk setiap pengguna. (Tipe: numerik)
-   `movie_id`: ID unik film yang diberi rating. (Tipe: numerik)
-   `rating`: Rating yang diberikan oleh pengguna, dengan skala 0.5 hingga 5.0. (Tipe: numerik)
-   `timestamp`: Waktu pemberian rating dalam format epoch time. (Tipe: numerik)

## Data Exploration/Exploratory Data Analysis (EDA)

Analisis data eksploratif dilakukan untuk memahami distribusi dan karakteristik data.

### Distribution of Movie Release Years

This bar chart, displaying the "Distribution of Movie Release Years," illustrates the temporal spread of films within the MovieLens 10M dataset.
<div style="text-align: center;">
  <img src="path/to/your/image_ef21c3.png" alt="Distribution of Movie Release Years">
</div>

**Key Observations:**
* **Increasing Trend:** There's a clear upward trend in the number of movies released over time, with a significant increase from the 1980s onwards.
* **Peak in Early 2000s:** The distribution peaks around the early 2000s, indicating that the majority of films in the dataset were released during this period.
* **Declining Frequency Post-Peak:** After this peak, there's a noticeable decline in the number of movies, suggesting that the dataset may not include a complete representation of very recent films.
* **Limited Older Content:** Films from before the 1940s are scarce, reflecting a smaller collection of older movies in the dataset.

**Implications for Recommendation Systems:**
This temporal distribution is crucial for time-aware recommendation systems, such as your "Dynamic Popularity" model. The high concentration of films in the late 1990s and early 2000s means that popularity metrics will be heavily influenced by movies from this era. This could lead to recommendations that predominantly feature films from this period, which might be beneficial for users interested in recent popular releases but less so for those seeking older or very contemporary content.

### Distribution of Movie Ratings

This bar chart illustrates the frequency of different movie ratings within the MovieLens 10M dataset.
<div style="text-align: center;">
  <img src="path/to/your/image_ef775a.png" alt="Distribution of Movie Ratings">
</div>

**Key Observations:**
* **Predominantly Positive Ratings**: The most frequent ratings are 4.0, 3.0, and 5.0 stars, indicating that users tend to give positive feedback.
* **Preference for Whole Numbers**: Whole-star ratings (e.g., 3.0, 4.0, 5.0) are significantly more common than half-star ratings (e.g., 2.5, 3.5, 4.5).
* **Peak at 4.0 Stars**: The 4.0-star rating has the highest count, approaching 3 million instances.

**Implications for Recommendation Systems**:
This distribution suggests that the dataset contains more information on what users **like** rather than what they dislike. This pattern supports the strategy of defining a "liked" movie as one rated 4 stars or higher for evaluation purposes, as this clearly represents a common positive interaction. This bias towards positive ratings can influence models to prioritize recommending items that have received high scores.

### Distribution of Ratings per User

This bar chart, titled "Distribution of Ratings per User," illustrates how many ratings each user has submitted in the MovieLens 10M dataset. The y-axis is on a logarithmic scale, which helps visualize the wide range of user activity.
<div style="text-align: center;">
  <img src="path/to/your/image_ef7f19.png" alt="Distribution of Ratings per User">
</div>

**Key Observations:**
* **Highly Skewed Distribution**: The distribution is extremely skewed, with a vast majority of users having submitted a very small number of ratings (concentrated on the left side of the chart).
* **Long Tail of Active Users**: Despite the large number of inactive users, there's a long "tail" extending to the right, indicating a small number of highly active users who have submitted thousands of ratings. The logarithmic scale effectively highlights these less frequent, but highly significant, users.
* **Sparse Interaction Data**: The shape of this distribution confirms that the user-item interaction matrix is likely very sparse, meaning most users have only rated a small fraction of the total movies available.

**Implications for Recommendation Systems:**
This distribution directly impacts Collaborative Filtering models. The presence of highly active users provides rich data for identifying patterns, while the large number of infrequent users contributes to the "cold-start" problem and data sparsity challenges. Models need to be robust enough to handle this imbalance, leveraging data from active users while still providing meaningful recommendations for less active ones.

### Distribution of Ratings per Movie

This bar chart, titled "Distribution of Ratings per Movie," illustrates the frequency of ratings received by each movie in the MovieLens 10M dataset. The y-axis is on a logarithmic scale to better represent the wide range of rating counts.
<div style="text-align: center;">
  <img src="path/to/your/image_ef82bc.png" alt="Distribution of Ratings per Movie">
</div>

**Key Observations:**
* **Highly Skewed Distribution**: The distribution is heavily skewed, with a large number of movies receiving very few ratings (concentrated on the left side of the chart). This indicates that most movies in the dataset are relatively unpopular.
* **"Long Tail" of Popular Movies**: Conversely, there's a "long tail" extending to the right, representing a smaller number of highly popular movies that have accumulated tens of thousands of ratings. The logarithmic scale makes these more visible.
* **Item Cold-Start**: This distribution highlights the "item cold-start problem," where new or less popular movies have insufficient rating data to generate effective recommendations.

**Implications for Recommendation Systems:**
This skewed distribution significantly affects the performance of collaborative filtering models. Models may exhibit a **'popularity bias'**, tending to recommend well-known movies more frequently due to the abundant rating data available for them. While this is good for mainstream users, it can limit the discoverability of niche content. This underscores the challenge of balancing recommendations between popular and less popular items.

### Top 10 Most Common Movie Genres

This bar chart provides a clear visualization of genre distribution within the MovieLens 10M dataset.
<div style="text-align: center;">
  <img src="path/to/your/image_ef1967.png" alt="Top 10 Most Common Movie Genres">
</div>

**Key Observations:**
* **Dominant Genres**: "Drama" and "Comedy" are overwhelmingly the most frequent genres, indicating a significant volume of movies categorized under them.
* **Skewed Distribution**: The data shows a skewed distribution, with a few genres (like Drama and Comedy) appearing much more frequently than others (e.g., Sci-Fi, Fantasy).
* **Implication of Popularity Bias**: This skewness creates a 'popularity bias'. Recommendation systems built on this genre data might disproportionately suggest Drama and Comedy films due to their high representation, potentially limiting the discovery of more diverse or niche content.

This analysis highlights that while the dataset is extensive, its genre distribution suggests that a purely genre-based Content-Based filtering approach might lead to overspecialization in recommendations.

## Data Preparation

### Data Preprocessing & Feature Engineering: Interpretasi

These preprocessing steps strategically prepare the MovieLens 10M dataset for our two distinct recommendation models.

First, the `ratings` and `movies` DataFrames are **merged into a single `df`** on `movie_id`, consolidating all relevant information.

For **Content-Based Filtering (TF-IDF on Titles)**:
The `title` column in `movies` is transformed into `clean_title` by **removing the release year**. This ensures that our TF-IDF analysis focuses solely on **descriptive keywords within the title**, capturing thematic similarities rather than temporal ones, which is a key novelty of this approach.

For **Collaborative Filtering (Dynamic Popularity)**:
The `timestamp` column in `df` is **converted to datetime objects**. This is crucial for the "Dynamic Popularity" model, enabling **time-based analysis of user interactions**. Unlike traditional SVD (which often discards timestamps), this conversion allows the model to identify and recommend movies based on recent trends and popularity shifts, providing a distinct advantage for current relevance. The data spans a specific range, indicating its readiness for temporal analysis.

### Pembersihan Awal (Base Prep)

Beberapa langkah persiapan data dilakukan untuk memastikan data siap digunakan untuk pemodelan. Urutan proses ini sangat penting untuk menjamin kualitas data yang masuk ke dalam model.

* **Menghapus Kolom `timestamp` (untuk Collaborative Filtering SVD Lama)**: Dalam proyek asli temanmu, kolom `timestamp` dihapus dari data rating karena waktu pemberian rating tidak relevan untuk model SVD baseline yang dibangun. Alasan utamanya adalah untuk menyederhanakan dataset dan mengurangi penggunaan memori, karena fokus utama adalah pada preferensi (rating) itu sendiri, bukan pada kapan preferensi itu dicatat.

### Filter dan Penggabungan Data

* **Sampling Pengguna Aktif**: Untuk efisiensi komputasi, pemodelan difokuskan pada pengguna yang paling aktif. Pengguna yang telah memberikan rating pada minimal 500 film dipilih. Alasan dari langkah ini adalah untuk memastikan model dilatih pada pengguna dengan data preferensi yang kaya dan historis, sehingga pola seleranya lebih mudah dipelajari oleh model *collaborative filtering*.
* **Penggabungan Data**: Data rating yang telah disaring (`ratings_sample`) digabungkan dengan data film (`movies`) berdasarkan `movie_id`. Tujuannya adalah untuk menciptakan satu DataFrame utama yang berisi semua informasi yang dibutuhkan (user, film, rating, genre) dalam satu tempat.

### Persiapan Data untuk Content-Based Filtering

* **Membuat Lookup Table Film**: Sebuah DataFrame baru (`movies_df`) dibuat dari data film yang unik, dengan `movie_id` ditetapkan sebagai indeks (`set_index`). Ini berfungsi sebagai kamus atau *lookup table* yang efisien untuk mengambil informasi film berdasarkan ID-nya.
* **Transformasi Fitur `genres` (Asli Teman)**: Karakter `|` pada kolom `genres` diganti dengan spasi (misal: "Adventure|Sci-Fi" menjadi "Adventure Sci-Fi"). Langkah ini penting agar setiap genre bisa dianggap sebagai kata atau "token" terpisah oleh `TfidfVectorizer`. (Catatan: Dalam kebaruan proyek ini, fokus Content-Based adalah pada judul, tetapi langkah ini mungkin masih ada dari project teman).
* **Ekstraksi Fitur dengan TF-IDF (Asli Teman)**: Kolom `genres` yang sudah bersih diubah menjadi matriks numerik **TF-IDF**. Proses ini tidak hanya menghitung kemunculan sebuah genre, tetapi juga memberinya bobot berdasarkan seberapa unik genre tersebut di seluruh dataset. Hasilnya adalah representasi matematis dari "profil genre" setiap film, yang siap digunakan untuk menghitung kemiripan. (Catatan: Dalam kebaruan proyek ini, TF-IDF diterapkan pada *judul*, bukan genre).
* **Pemetaan Judul ke ID Film**: Dibuat sebuah `pandas Series` bernama `indices` yang berfungsi sebagai "kamus terbalik" yang memetakan **judul film ke `movie_id`**. Tujuannya adalah agar fungsi rekomendasi dapat dengan cepat menemukan indeks sebuah film di dalam matriks kemiripan (`cosine_sim`) hanya dengan menggunakan judulnya sebagai input.

### Pemisahan Data (Train-Test Split)

* **Pemisahan Data**: `df` yang telah digabungkan dibagi menjadi data latih (80%) dan data uji (20%). Ini penting agar kita bisa melatih model pada satu set data dan menguji performanya pada set data lain yang belum pernah dilihat sebelumnya.

## Modeling

Dua model sistem rekomendasi dikembangkan untuk menyelesaikan permasalahan yang telah didefinisikan. Setiap model menggunakan pendekatan yang berbeda secara fundamental untuk menghasilkan rekomendasi.

### 1. Content-Based Filtering

Model ini merekomendasikan film berdasarkan kemiripan atribut atau "konten" dari film itu sendiri, dalam kasus ini adalah **kata kunci dari judul film**.

**Cara Kerja:**
Model ini bekerja seperti "pencocokan profil" berdasarkan kata kunci judul. Pada tahap *Data Preparation*, kita telah mengubah judul setiap film menjadi sebuah profil numerik (matriks TF-IDF) setelah membersihkannya dari tahun rilis.
-   **Cosine Similarity**: Metrik ini kemudian digunakan untuk menghitung skor kemiripan antara "profil judul" dari semua pasangan film. Skornya berkisar dari 0 (sama sekali tidak mirip) hingga 1 (identik secara judul).
-   **Top-N Recommendations**: Ketika Anda menyukai sebuah film, sistem akan mencari film-film lain dengan profil judul yang paling mirip berdasarkan skor *cosine similarity* tertinggi, lalu menyajikannya sebagai rekomendasi teratas.

**Contoh Output:**
Berikut adalah 5 rekomendasi teratas untuk film 'Toy Story (1995)', yang menunjukkan film-film dengan kata kunci judul serupa.
```text
--- Content-Based Recommendations for 'Toy Story (1995)' ---
                    title                       genres      clean_title
movie_id
2028     Toy Story 2 (1999)  Adventure|Animation|Children|Comedy|Fantasy  Toy Story 2
61085    Toy Story 3 (2010)  Adventure|Animation|Children|Comedy|Fantasy  Toy Story 3
