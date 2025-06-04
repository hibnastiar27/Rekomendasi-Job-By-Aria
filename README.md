# Laporan Proyek Machine Learning - Nur Aria Hibnastiar

## Project Overview

Pencarian pekerjaan yang sesuai dengan keahlian dan preferensi pengguna merupakan tantangan yang umum dihadapi dalam dunia kerja saat ini. Sistem rekomendasi pekerjaan berbasis konten dapat membantu mengatasi masalah ini dengan memberikan rekomendasi lowongan yang relevan berdasarkan analisis deskripsi pekerjaan dan preferensi pengguna.

Metode content-based filtering menggunakan TF-IDF dan cosine similarity terbukti efektif dalam menyaring dan merekomendasikan pekerjaan yang sesuai [1]. Proyek ini bertujuan membangun sistem rekomendasi pekerjaan yang mampu memberikan rekomendasi relevan dan personal.

## Business Understanding

### Problem Statements

- Pencari kerja kesulitan menemukan lowongan yang relevan di antara banyak pilihan.
- Sistem pencarian saat ini belum mampu memberikan rekomendasi pekerjaan secara otomatis dan personal.

### Goals

- Membangun sistem rekomendasi berbasis konten yang memberikan lowongan relevan berdasarkan deskripsi pekerjaan dan preferensi pengguna.
- Mengoptimalkan rekomendasi dengan evaluasi metrik relevansi dan cakupan.


### Solution Statements

- Gunakan content-based filtering dengan TF-IDF dan cosine similarity untuk merekomendasikan pekerjaan yang mirip secara konten.
- Evaluasi sistem menggunakan metrik Precision@k untuk mengukur relevansi rekomendasi dan Coverage untuk mengukur cakupan sistem.

## Data Understanding

Dataset yang digunakan berisi **9.981** entri pekerjaan yang merupakan subset dari data asli sekitar 1 juta lebih entri. Data ini telah melalui proses pembersihan, termasuk penghapusan 19 data duplikat sehingga memastikan tidak ada data yang sama secara identik dalam dataset. Selain itu, seluruh fitur pada dataset ini tidak memiliki nilai kosong (null), sehingga kualitas data cukup baik untuk pemodelan.

Dataset ini memiliki 23 Fitur sebagai berikut :

1. **Job Id**: ID unik untuk setiap lowongan kerja.
2. **Experience**: Jumlah tahun pengalaman kerja yang dibutuhkan atau diutamakan.
3. **Qualifications**: Kualifikasi pendidikan yang diperlukan.
4. **Salary Range**: Rentang gaji yang ditawarkan.
5. **Location**: Kota atau area lokasi pekerjaan.
6. **Country**: Negara tempat pekerjaan berada.
7. **Latitude**: Koordinat lintang lokasi pekerjaan.
8. **Longitude**: Koordinat bujur lokasi pekerjaan.
9. **Work Type**: Jenis pekerjaan (misalnya penuh waktu, paruh waktu, kontrak).
10. **Company Size**: Ukuran perusahaan perekrut.
11. **Job Posting Date**: Tanggal lowongan diumumkan.
12. **Preference**: Preferensi khusus untuk pelamar (misalnya Hanya Pria, Hanya Wanita, atau Keduanya).
13. **Contact Person**: Nama orang yang dapat dihubungi untuk lowongan tersebut.
14. **Contact**: Informasi kontak untuk pertanyaan terkait pekerjaan.
15. **Job Title**: Judul atau posisi pekerjaan yang ditawarkan.
16. **Role**: Kategori atau peran pekerjaan.
17. **Job Portal**: Platform atau situs tempat lowongan diunggah.
18. **Job Description**: Deskripsi rinci tentang tugas dan persyaratan pekerjaan.
19. **Benefits**: Informasi tentang tunjangan atau manfaat pekerjaan.
20. **Skills**: Keahlian atau kualifikasi yang dibutuhkan.
21. **Responsibilities**: Tanggung jawab dan tugas khusus pekerjaan.
22. **Company Name**: Nama perusahaan perekrut.
23. **Company Profile**: Ringkasan latar belakang dan misi perusahaan.

dari 23 fitur tersebut akan diambil 7 fitur yang akan digunakan sebagai rekomendasi job :

1. **Job Title**: Judul pekerjaan, seperti “Network Engineer”, “Data Entry Clerk”, dll.
2. **Job Description**: Deskripsi detail mengenai pekerjaan.
3. **skills**: Keterampilan yang diperlukan untuk pekerjaan tersebut.
4. **Responsibilities**: Tanggung jawab utama yang harus dilakukan oleh pemegang pekerjaan.
5. **Qualifications**: Kualifikasi pendidikan atau sertifikasi yang dibutuhkan.
6. **location**: Lokasi pekerjaan tersebut tersedia.
7. **Work Type**: Tipe pekerjaan, misalnya Full-Time, Part-Time, Contract, Temporary, atau Intern.

Dataset ini merupakan representasi pekerjaan dengan variasi lokasi dan tipe kerja yang beragam, serta informasi detail yang dapat dimanfaatkan untuk membangun sistem rekomendasi pekerjaan berbasis konten. Dataset asli dapat diunduh di [Kaggle Job Description Dataset](https://www.kaggle.com/datasets/ravindrasinghrana/job-description-dataset).

Meski belum dilakukan visualisasi eksplorasi data secara mendalam, analisis awal terhadap kondisi data menunjukkan dataset ini cukup bersih dan siap digunakan untuk tahap pemodelan selanjutnya.

## Data Preparation

Tahapan *data preparation* dilakukan untuk memastikan data yang digunakan dalam pemodelan bersih, relevan, dan siap diproses. Berikut langkah-langkah yang dilakukan:

1. **Sampling Data**
   Dilakukan *sampling* sebanyak 10.000 data secara acak untuk efisiensi proses dan mengurangi beban komputasi.

2. **Seleksi Fitur**
   Mengacu pada data understanding diatas yaitu hanya dipilih 7 fitur yang relevan: `Job Title`, `Job Description`, `skills`, `Responsibilities`, `Qualifications`, `location`, dan `Work Type`.

3. **Menghapus Duplikat dan Nilai Kosong**
   Data duplikat dan baris yang memiliki nilai kosong dihapus agar hasil pemodelan lebih akurat dan bersih.

4. **Penggabungan Kolom Teks**
   Beberapa kolom teks seperti `Job Description`, `skills`, `Responsibilities`, `Qualifications`, `location`, dan `Work Type` digabungkan menjadi satu kolom teks gabungan. Tujuannya agar seluruh informasi penting dari pekerjaan tersedia dalam satu representasi.

5. **Pembersihan Teks**
   Teks dibersihkan dengan mengubah huruf menjadi kecil, menghapus angka, dan menghilangkan tanda baca untuk menjaga konsistensi dan mengurangi noise.

6. **Ekstraksi Fitur dengan TF-IDF**
   Data teks yang telah dibersihkan diubah menjadi representasi numerik menggunakan metode **TF-IDF (Term Frequency-Inverse Document Frequency)**.
   Penggunaan TF-IDF bertujuan untuk menangkap kata-kata penting dalam dokumen dan membantu model mengenali pola dalam data teks.
   Parameter `max_features=10000` digunakan untuk mempertahankan kata-kata paling informatif.

## Modeling

Pada proyek ini, sistem rekomendasi pekerjaan dikembangkan menggunakan pendekatan **Content-Based Filtering**, yang memanfaatkan deskripsi dan konten pekerjaan untuk menghasilkan rekomendasi serupa. Pendekatan ini tidak memerlukan data historis pengguna, melainkan hanya menggunakan informasi dari masing-masing lowongan kerja itu sendiri.

### Content-Based Filtering

#### 1. **Representasi Teks**

Beberapa kolom penting seperti `Job Description`, `skills`, `Responsibilities`, `Qualifications`, `location`, dan `Work Type` digabungkan menjadi satu kolom `combined_text`. Setelah dibersihkan, teks diubah menjadi representasi numerik menggunakan metode **TF-IDF (Term Frequency-Inverse Document Frequency)** dengan maksimal 10.000 fitur. Proses ini dijelaskan pada bagian *Data Preparation*.

#### 2. **Cosine Similarity**

Setelah teks diubah menjadi matriks TF-IDF, dihitung **cosine similarity** antar pekerjaan. Cosine similarity mengukur sejauh mana dua pekerjaan mirip berdasarkan arah vektor TF-IDF mereka. Semakin besar nilai cosine similarity (mendekati 1), semakin mirip kontennya.

#### 3. **Fungsi Rekomendasi**

Saat pengguna memasukkan kata kunci dalam *job title*, sistem:

* Mencari pekerjaan dengan judul yang cocok (mengandung kata kunci).
* Menghitung skor kemiripan antara pekerjaan tersebut dengan seluruh pekerjaan lain.
* Mengembalikan **Top-N rekomendasi** dengan skor kemiripan tertinggi, beserta informasi lokasi dan jenis pekerjaan.

#### Contoh penggunaan rekomendasi

Jika pengguna mencari dengan kata kunci `Software Engine`, sistem mengembalikan hasil seperti:

| Job Title         | Location | Work Type | Similarity (%) |
| ----------------- | -------- | --------- | -------------- |
| Software Engineer | Belgrade | Intern    | 97.77          |
| Software Engineer | Harare   | Contract  | 97.68          |
| Software Engineer | Ankara   | Temporary | 97.63          |
| Software Engineer | Belgrade | Part-Time | 97.57          |
| Software Engineer | Prague   | Part-Time | 97.56          |

### Kelebihan:

* Memberikan rekomendasi berdasarkan isi deskripsi pekerjaan secara langsung.
* Tidak membutuhkan data pengguna (cocok untuk cold-start pada user).
* Sederhana, transparan, dan mudah diimplementasikan.

### Kekurangan:

* Hanya mempertimbangkan konten pekerjaan, tidak mempertimbangkan preferensi pengguna personal.
* Sensitif terhadap deskripsi yang kurang informatif atau tidak konsisten.
* Tidak mempertimbangkan popularitas atau interaksi historis.

---

## Evaluation

Evaluasi sistem rekomendasi pekerjaan ini dilakukan dengan fokus pada kualitas rekomendasi yang diberikan menggunakan dua metrik utama, yaitu **Precision\@k** dan **Coverage**. Metrik-metrik ini dipilih karena sesuai untuk mengukur efektivitas sistem rekomendasi dalam konteks *information retrieval* dan relevansi hasil rekomendasi.

### Metrik Evaluasi

1. **Precision\@k**
   Precision\@k mengukur proporsi pekerjaan yang relevan di antara *k* rekomendasi teratas yang diberikan oleh sistem.
   Rumusnya:

   $$\text{Precision@k} = \frac{\text{Jumlah rekomendasi relevan dalam k teratas}}{k}$$

   Metrik ini menilai ketepatan rekomendasi yang disajikan kepada pengguna, dimana nilai lebih tinggi menunjukkan relevansi yang lebih baik.

2. **Coverage**
   Coverage mengukur proporsi query pencarian pekerjaan yang berhasil diberikan rekomendasi oleh sistem dari seluruh query yang diuji.
   Rumusnya:

   $$\text{Coverage} = \frac{\text{Jumlah query dengan rekomendasi valid}}{\text{Total query}} \times 100\%$$

   Metrik ini menilai sejauh mana sistem mampu memberikan rekomendasi untuk berbagai jenis pekerjaan yang dicari pengguna.

### Hasil Evaluasi

| Query             | Precision\@5    |
| ----------------- | --------------- |
| software engineer | 1.00            |
| data analyst      | 1.00            |
| marketing manager | 1.00            |
| project manager   | 1.00            |
| data scientist    | 0.00            |
| ui/ux             | Tidak ditemukan |

* **Rata-rata Precision\@5:** 0.80
* **Coverage:** 83.33%

Sistem mampu memberikan rekomendasi relevan dengan nilai Precision\@5 1.00 untuk sebagian besar query populer, menunjukkan akurasi tinggi dalam menampilkan pekerjaan terkait. Namun, untuk query seperti "ui/ux" dan "data scientist", sistem belum memberikan hasil rekomendasi yang memadai, sehingga menurunkan nilai precision dan coverage.

### Kesimpulan

Model rekomendasi pekerjaan berbasis content-based filtering ini telah berhasil memberikan rekomendasi yang relevan dengan tingkat akurasi tinggi untuk sebagian besar query. Coverage sebesar 83.33% menunjukkan bahwa sistem cukup luas dalam menyediakan rekomendasi untuk berbagai jenis pekerjaan. Meskipun demikian, ada ruang perbaikan terutama pada jenis pekerjaan dengan data lebih terbatas atau query yang kurang tepat, yang dapat menjadi fokus pengembangan selanjutnya untuk meningkatkan cakupan dan akurasi rekomendasi.

## Referensi:

[1] V. Lampos, N. Androutsopoulos, dan P. Preoţiuc-Pietro, "Content-based job recommendation systems: A review and comparative study," Journal of Information Processing & Management, vol. 56, no. 3, pp. 102–115, 2019.