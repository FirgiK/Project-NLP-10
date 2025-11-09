# Project-NLP-10

Analisis sentimen terhadap wacana publik mengenai isu Israel-Palestina di X, mengkorelasikan sentimen dengan keterlibatan unggahan/komentar.

---

## 1. Tujuan Proyek (Project Objective)

Tujuan dari proyek ini adalah untuk menganalisis sentimen publik di platform X (Twitter) terkait konflik Israel-Palestina. Proyek ini bertujuan untuk melatih sebuah model *machine learning* yang mampu mengklasifikasikan sebuah cuitan ke dalam tiga kategori sentimen: **Positif**, **Negatif**, atau **Netral**.

---

## 2. Alur Kerja (Workflow)

Proses analisis sentimen ini mengikuti alur kerja standar NLP:

1.  **Muat Data:** Mengimpor dataset `DataSet_X.csv` yang berisi cuitan mentah.
2.  **Pemahaman Data:** Menganalisis data, menghapus baris duplikat, dan menangani nilai yang hilang.
3.  **Pemrosesan Teks (NLP):** Membersihkan teks mentah melalui serangkaian langkah:
    * **Tokenisasi:** Memecah kalimat menjadi kata-kata.
    * **Normalisasi & Lemmatisasi:** Mengubah kata ke bentuk dasarnya (misal: "protests" -> "protest", dan "Israelis" -> "israel").
    * **Stopword Removal:** Menghapus kata-kata umum (seperti "the", "is") dan kata-kata bising spesifik domain (seperti "http", "rt").
4.  **Labeling Otomatis:** Menggunakan **VADER (SentimentIntensityAnalyzer)** untuk memberi label sentimen (Positif, Negatif, Netral) pada setiap cuitan sebagai data target (y).
5.  **Vectorization:** Mengubah teks yang sudah bersih (fitur/X) menjadi representasi numerik menggunakan **TF-IDF Vectorizer**.
6.  **Pelatihan & Evaluasi Model:** Membagi data (80% latih, 20% uji) dan melatih model klasifikasi untuk dievaluasi.

---

## 3. Tantangan & Iterasi (The Project Journey)

Dalam prosesnya, ditemukan beberapa tantangan signifikan yang memengaruhi performa model.

### Masalah Awal: Model Naive Bayes & Data Tidak Seimbang

Model pertama yang dicoba adalah `Multinomial Naive Bayes`. Hasil *confusion matrix* awal menunjukkan performa yang sangat buruk:

* Model hampir sepenuhnya mengabaikan kelas **"Netral"** (hanya 3 tebakan benar dari 219).
* Model sangat bias menebak **"Negatif"** (1214 dari 1478 total tebakan).

<img width="649" height="547" alt="unnamed" src="https://github.com/user-attachments/assets/3e3749fe-dffd-4d36-906e-99d4ff8f8291" />


Penyebabnya ada dua:
1.  **Data Tidak Seimbang:** Label dari VADER sangat tidak seimbang (3945 Negatif vs. 1093 Netral).
2.  **Stopword Agresif:** Daftar `custom_stop_words` awal menghapus kata-kata penting untuk sentimen (seperti 'good', 'love', 'evil', 'lie').

### Iterasi 1: Perbaikan Stopword & Model (Hasil Terbaik)

Langkah perbaikan pertama adalah merevisi daftar *stopword* untuk hanya menghapus kata-kata netral yang bising.

Selanjutnya, untuk mengatasi data tidak seimbang, model diganti dari `Multinomial Naive Bayes` menjadi **`LogisticRegression(class_weight='balanced')`**. Parameter ini secara otomatis memberi bobot lebih pada kelas minoritas ('Netral' & 'Positif') selama pelatihan.

**Hasilnya adalah peningkatan drastis:**
* Tebakan benar untuk **Netral** melonjak dari **3** menjadi **141**.
* Tebakan benar untuk **Positif** meningkat dari **189** menjadi **326**.
* Model tidak lagi bias dan mampu mengidentifikasi ketiga kelas dengan jauh lebih baik.

### Iterasi 2: Eksperimen N-grams

Model Regresi Logistik kemudian diuji dengan `ngram_range=(1, 2)` (menggunakan bigram/pasangan kata). Hasilnya, deteksi 'Negatif' meningkat (673), namun deteksi 'Netral' (102) dan 'Positif' (274) kembali menurun.

---

## 4. Hasil Akhir (Final Results)

Model dengan performa terbaik dan paling seimbang adalah:
* **Model:** `LogisticRegression(class_weight='balanced')`
* **Fitur:** `TfidfVectorizer(max_features=5000, ngram_range=(1, 1))`
* **Label:** Dihasilkan oleh VADER.

*Confusion matrix* akhir di bawah ini menunjukkan bahwa model ini adalah kompromi terbaik untuk mengidentifikasi ketiga sentimen dalam dataset yang tidak seimbang.

<img width="649" height="547" alt="download" src="https://github.com/user-attachments/assets/d260c4fc-2c0d-44bb-8596-6f98587d1517" />

---

## 5. Cara Menjalankan (How to Run)

1.  Pastikan file `DataSet_X.csv` berada di *path* Google Drive yang sesuai (didefinisikan di Cell 4).
2.  Jalankan semua cell di *notebook* `test.ipynb` secara berurutan.
