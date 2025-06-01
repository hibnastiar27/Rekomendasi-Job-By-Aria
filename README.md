# Laporan Proyek Machine Learning - Nur Aria Hibnastiar

## Project Overview

Pencarian pekerjaan yang sesuai dengan keahlian dan preferensi pengguna merupakan tantangan yang umum dihadapi dalam dunia kerja saat ini. Sistem rekomendasi pekerjaan berbasis konten dapat membantu mengatasi masalah ini dengan memberikan rekomendasi lowongan yang relevan berdasarkan analisis deskripsi pekerjaan dan preferensi pengguna.

Metode content-based filtering menggunakan teknik seperti TF-IDF dan cosine similarity telah terbukti efektif dalam menyaring dan merekomendasikan pekerjaan yang sesuai [1]. Proyek ini bertujuan mengembangkan sistem rekomendasi pekerjaan yang menggabungkan pendekatan content-based filtering dan klasifikasi untuk memprediksi tipe pekerjaan, sehingga meningkatkan akurasi dan personalisasi rekomendasi.


## Business Understanding

### Problem Statements

* Pencari kerja kesulitan menemukan lowongan yang sesuai dengan keahlian dan preferensi mereka di antara banyaknya pilihan.
* Tidak ada sistem rekomendasi otomatis yang mampu membantu pencari kerja dengan memberikan lowongan yang relevan secara efektif.

### Goals

* Membangun sistem rekomendasi pekerjaan berbasis konten yang menyajikan lowongan kerja relevan berdasarkan deskripsi pekerjaan dan preferensi pengguna.
* Mengembangkan model klasifikasi yang dapat memprediksi tipe pekerjaan berdasarkan teks deskripsi untuk memberikan informasi tambahan bagi pengguna.

### Solution Statements

* Menggunakan content-based filtering dengan TF-IDF dan cosine similarity untuk merekomendasikan pekerjaan yang mirip secara konten.
* Membangun model klasifikasi Random Forest untuk memprediksi tipe pekerjaan berdasarkan deskripsi pekerjaan.

## Data Understanding

Dataset yang digunakan berisi **9.981** entri pekerjaan yang merupakan subset dari data asli sekitar 1 juta lebih entri. Data ini telah melalui proses pembersihan, termasuk penghapusan 19 data duplikat sehingga memastikan tidak ada data yang sama secara identik dalam dataset. Selain itu, seluruh fitur pada dataset ini tidak memiliki nilai kosong (null), sehingga kualitas data cukup baik untuk pemodelan.

Dataset ini memiliki 7 fitur utama yang merepresentasikan deskripsi pekerjaan secara komprehensif, yaitu:

* **Job Title**: Judul pekerjaan, seperti “Network Engineer”, “Data Entry Clerk”, dll.
* **Job Description**: Deskripsi detail mengenai pekerjaan.
* **skills**: Keterampilan yang diperlukan untuk pekerjaan tersebut.
* **Responsibilities**: Tanggung jawab utama yang harus dilakukan oleh pemegang pekerjaan.
* **Qualifications**: Kualifikasi pendidikan atau sertifikasi yang dibutuhkan.
* **location**: Lokasi pekerjaan tersebut tersedia.
* **Work Type**: Tipe pekerjaan, misalnya Full-Time, Part-Time, Contract, Temporary, atau Intern.

Dataset ini merupakan representasi pekerjaan dengan variasi lokasi dan tipe kerja yang beragam, serta informasi detail yang dapat dimanfaatkan untuk membangun sistem rekomendasi pekerjaan berbasis konten. Dataset asli dapat diunduh di [Kaggle Job Description Dataset](https://www.kaggle.com/datasets/ravindrasinghrana/job-description-dataset).

Meski belum dilakukan visualisasi eksplorasi data secara mendalam, analisis awal terhadap kondisi data menunjukkan dataset ini cukup bersih dan siap digunakan untuk tahap pemodelan selanjutnya.

## Data Preparation

Tahap persiapan data dimulai dengan penggabungan beberapa kolom teks yang berisi informasi penting seperti keterampilan (`skills`), deskripsi pekerjaan (`Job Description`), tanggung jawab (`Responsibilities`), kualifikasi (`Qualifications`), lokasi (`location`), dan jenis pekerjaan (`Work Type`) menjadi satu kolom gabungan. Penggabungan ini dilakukan agar seluruh informasi relevan dari setiap fitur dapat dianalisis secara terpadu dalam satu sumber data teks.

Selanjutnya dilakukan pembersihan data teks yang meliputi pengubahan seluruh teks menjadi huruf kecil untuk menjaga konsistensi, penghapusan angka yang dianggap tidak relevan, dan penghilangan tanda baca untuk mengurangi gangguan atau noise pada data teks.

Untuk target prediksi yaitu jenis pekerjaan (`Work Type`), dilakukan transformasi label kategorikal ke bentuk numerik menggunakan teknik encoding sehingga dapat diproses oleh algoritma pembelajaran mesin.

Representasi teks hasil pembersihan tersebut kemudian diubah menjadi bentuk numerik menggunakan metode TF-IDF (Term Frequency-Inverse Document Frequency). Dalam proses ini, digunakan pengaturan `max_features` sebanyak 10.000 fitur. Artinya, model hanya mengambil 10.000 kata atau token paling signifikan (berdasarkan bobot TF-IDF tertinggi) dari seluruh korpus teks. Pembatasan fitur ini bertujuan untuk mengurangi kompleksitas model sekaligus menghilangkan kata-kata yang kurang penting atau sangat jarang muncul, sehingga mempercepat proses pelatihan dan mengurangi risiko overfitting.

Metode TF-IDF ini efektif dalam mengekstraksi fitur dari data teks sehingga model dapat mengenali pola-pola penting. Selanjutnya, dihitung matriks kemiripan (similarity) antar data menggunakan cosine similarity yang menjadi dasar dalam membangun sistem rekomendasi pekerjaan berbasis kesamaan konten.

Seluruh proses persiapan ini bertujuan untuk memastikan data yang digunakan bersih, konsisten, dan memiliki representasi fitur yang tepat agar hasil pemodelan dan rekomendasi dapat optimal.

## Modeling


Pada proyek ini, sistem rekomendasi pekerjaan dibangun menggunakan dua pendekatan utama: content-based filtering dan klasifikasi Work Type. Kedua metode ini bertujuan memberikan rekomendasi pekerjaan yang relevan berdasarkan deskripsi pekerjaan dan fitur terkait lainnya.

### 1. Content-Based Filtering

Pendekatan pertama menggunakan teknik content-based filtering dengan memanfaatkan TF-IDF vectorization dan cosine similarity untuk mengukur kemiripan antar deskripsi pekerjaan.

* **TF-IDF Vectorization**: Semua informasi terkait pekerjaan yang relevan, seperti skills, deskripsi pekerjaan, tanggung jawab, kualifikasi, lokasi, dan jenis pekerjaan, digabungkan menjadi satu kolom teks bernama `combined_text`. Kemudian, TF-IDF Vectorizer digunakan untuk mengubah teks ini menjadi representasi numerik dengan fitur maksimal sebanyak 10.000 untuk menangkap konteks dan bobot kata penting dalam data.
* **Cosine Similarity**: Matriks TF-IDF digunakan untuk menghitung kemiripan antar pekerjaan berdasarkan sudut cosine antara vektor TF-IDF mereka.
* **Rekomendasi**: Ketika pengguna memasukkan job title, sistem akan mencari pekerjaan dengan judul yang mengandung kata kunci tersebut. Setelah menemukan pekerjaan yang cocok, sistem menghitung skor kemiripan dengan semua pekerjaan lain dan mengembalikan rekomendasi Top-N pekerjaan yang paling mirip, lengkap dengan informasi lokasi, jenis pekerjaan, dan persentase kemiripan.

Sebagai contoh, input `Software Engine` menghasilkan rekomendasi seperti berikut:

| Job Title         | Location | Work Type | Similarity (%) |
| ----------------- | -------- | --------- | -------------- |
| Software Engineer | Belgrade | Intern    | 97.77          |
| Software Engineer | Harare   | Contract  | 97.68          |
| Software Engineer | Ankara   | Temporary | 97.63          |
| Software Engineer | Belgrade | Part-Time | 97.57          |
| Software Engineer | Prague   | Part-Time | 97.56          |

**Kelebihan**:

* Memberikan rekomendasi berdasarkan konten deskriptif pekerjaan secara langsung.
* Mudah dipahami dan diimplementasikan.
* Tidak memerlukan data historis pengguna.

**Kekurangan**:

* Sensitif terhadap kualitas teks yang ada; kata-kata umum bisa mempengaruhi hasil.
* Tidak memperhitungkan preferensi pengguna secara eksplisit selain input job title.
* Kurang efektif untuk menangani cold-start jika deskripsi kurang informatif.

---

### 2. Klasifikasi Work Type Berdasarkan Konten Pekerjaan

Pendekatan kedua adalah membangun model klasifikasi untuk memprediksi jenis pekerjaan (`Work Type`) berdasarkan deskripsi gabungan (`combined_text`).

* **Data Split**: Data dibagi menjadi data latih (80%) dan data uji (20%) untuk validasi model.
* **Pipeline Model**: Pipeline terdiri dari TF-IDF Vectorizer (dengan maksimal fitur 10.000 dan stop words bahasa Inggris) dan Random Forest Classifier (50 estimator, random state=27).
* **Pelatihan Model**: Pipeline ini dilatih untuk mengklasifikasikan jenis pekerjaan ke dalam kategori seperti Full-Time, Part-Time, Contract, Intern, Temporary, dan lainnya.

Model klasifikasi ini dapat digunakan untuk mengkategorikan pekerjaan baru yang belum memiliki label `Work Type`, sehingga membantu dalam memfilter dan memberikan rekomendasi yang lebih spesifik berdasarkan jenis pekerjaan yang diinginkan oleh pengguna.

**Kelebihan**:

* Mampu mengotomasi pengklasifikasian jenis pekerjaan berdasarkan deskripsi secara akurat.
* Dapat meningkatkan personalisasi rekomendasi dengan memfilter berdasarkan jenis pekerjaan.
* Random Forest cukup tangguh terhadap overfitting dan dapat menangani fitur TF-IDF yang sparse.

**Kekurangan**:

* Membutuhkan data berlabel yang cukup untuk pelatihan.
* Model klasifikasi tidak langsung menghasilkan rekomendasi pekerjaan, melainkan sebagai fitur pendukung.
* Kinerja model bergantung pada kualitas dan keberagaman data latih.

---

### Output Sistem Rekomendasi

Sistem utama menggunakan content-based filtering untuk memberikan rekomendasi pekerjaan Top-N dengan tingkat kemiripan tertinggi berdasarkan input job title pengguna. Klasifikasi jenis pekerjaan menjadi komponen tambahan yang memungkinkan penyaringan rekomendasi lebih lanjut sesuai preferensi pengguna terkait jenis pekerjaan.

## Evaluation

Dalam proyek ini, evaluasi performa model klasifikasi jenis pekerjaan (`Work Type`) dilakukan menggunakan beberapa metrik utama, yaitu **akurasi (accuracy)**, **precision**, **recall**, dan **f1-score**. Metrik-metrik ini dipilih karena mampu memberikan gambaran menyeluruh mengenai kualitas prediksi model dalam konteks klasifikasi multi-kelas.

### Metrik Evaluasi

1. **Akurasi (Accuracy)**
   Akurasi mengukur proporsi prediksi yang benar dari keseluruhan sampel.

   $$\text{Accuracy} = \frac{\text{Jumlah prediksi benar}}{\text{Total data uji}}$$

   Metrik ini memberikan gambaran umum performa model, namun kurang sensitif pada ketidakseimbangan kelas.

2. **Precision**
   Precision mengukur ketepatan prediksi positif untuk masing-masing kelas, yaitu rasio prediksi positif yang benar-benar relevan:

   $$\text{Precision} = \frac{TP}{TP + FP}$$

   Di mana $TP$ adalah True Positives dan $FP$ adalah False Positives.

3. **Recall**
   Recall mengukur kemampuan model dalam menangkap seluruh instance positif sebenarnya:

   $$\text{Recall} = \frac{TP}{TP + FN}$$

   Di mana $FN$ adalah False Negatives.

4. **F1-Score**
   F1-score merupakan rata-rata harmonis dari precision dan recall, memberikan keseimbangan antara keduanya:

   $$F1 = 2 \times \frac{\text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}}$$


### Metrics Classification Report (Test Set)

| Class     | Precision | Recall | F1-score | Support |
| --------- | --------- | ------ | -------- | ------- |
| Contract  | 1.00      | 1.00   | 1.00     | 432     |
| Full-Time | 0.99      | 1.00   | 1.00     | 398     |
| Intern    | 1.00      | 1.00   | 1.00     | 409     |
| Part-Time | 1.00      | 1.00   | 1.00     | 365     |
| Temporary | 1.00      | 1.00   | 1.00     | 393     |

* **Overall Accuracy:** 99.85%
* Model menunjukkan performa sangat baik untuk klasifikasi jenis pekerjaan berdasarkan teks deskriptif.

### Hasil Evaluasi

Model Random Forest yang dilatih dengan fitur teks hasil TF-IDF mencapai akurasi **99.85%** pada data uji sebanyak 1.997 contoh. Classification report menunjukkan nilai precision, recall, dan f1-score yang mendekati 1 pada semua kelas `Work Type` (Contract, Full-Time, Intern, Part-Time, Temporary), yang menandakan performa model yang sangat baik dan seimbang di setiap kategori.

### Kesimpulan

Metrik evaluasi yang tinggi membuktikan bahwa model ini efektif dalam mengklasifikasikan jenis pekerjaan berdasarkan deskripsi pekerjaan dan atribut terkait. Oleh karena itu, model sangat layak digunakan sebagai bagian dari sistem rekomendasi pekerjaan yang akurat dan responsif terhadap preferensi pengguna.


## Referensi:

[1] V. Lampos, N. Androutsopoulos, dan P. Preoţiuc-Pietro, "Content-based job recommendation systems: A review and comparative study," Journal of Information Processing & Management, vol. 56, no. 3, pp. 102–115, 2019.