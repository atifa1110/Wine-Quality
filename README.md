### Laporan Proyek Machine Learning - Atifa Fiorenza

### Domain Proyek
Kualitas wine merupakan salah satu faktor penentu nilai jual dan daya saing produk di industri minuman beralkohol. Penilaian kualitas biasanya dilakukan melalui uji rasa (sensory evaluation) oleh panelis terlatih. Meski metode ini lazim digunakan, prosesnya memerlukan waktu, biaya, dan tetap memiliki unsur subjektivitas.

Penelitian Cortez et al. (2009) menunjukkan bahwa kualitas wine dapat dimodelkan secara objektif menggunakan parameter fisikokimia seperti fixed acidity, volatile acidity, citric acid, kadar alkohol, dan kadar gula sisa (residual sugar). Melalui pendekatan data mining dan machine learning, hubungan antara variabel-variabel kimia ini dengan penilaian kualitas wine dapat dianalisis, sehingga memungkinkan prediksi kualitas tanpa harus melalui pengujian rasa manual.

Dataset yang digunakan pada proyek ini berasal dari penelitian tersebut dan berfokus pada red wine “Vinho Verde” asal Portugal. Dengan memanfaatkan teknik klasifikasi seperti Logistic Regression, Random Forest, XGBoost, SVM, dan KNN, diharapkan dapat dibangun model prediksi yang membantu produsen wine dalam mengontrol kualitas produk secara cepat, efisien, dan terukur berdasarkan parameter kimiawi yang mudah diukur di laboratorium.

Referensi:
Cortez, P., Cerdeira, A., Almeida, F., Matos, T., & Reis, J. (2009). Modeling wine preferences by data mining from physicochemical properties. Decision Support Systems, 47(4), 547–553.

----

### Business Understanding
##### Problem Statements
1. Apakah terdapat hubungan signifikan antara parameter fisikokimia wine (misalnya fixed acidity, volatile acidity, citric acid, alkohol) dan kualitas wine?
2. Bagaimana cara memprediksi kualitas red wine secara akurat hanya dengan menggunakan data kimiawi?
3. Algoritma klasifikasi mana yang memberikan performa terbaik untuk prediksi kualitas wine?

##### Goals
1. Mengidentifikasi fitur-fitur kimia yang memiliki pengaruh signifikan terhadap kualitas wine.
2. Membangun model klasifikasi yang dapat memprediksi kualitas wine dengan tingkat akurasi yang tinggi.
3.Membandingkan beberapa algoritma untuk menemukan model dengan performa terbaik.

##### Solution Statement
1. Menggunakan beberapa algoritma klasifikasi seperti Logistic Regression, Random Forest, XGBoost, SVM, dan KNN untuk membangun model prediksi.
2. Melakukan hyperparameter tuning untuk meningkatkan performa model terbaik berdasarkan metrik evaluasi seperti akurasi, precision, recall, dan F1-score.

---

### Data Understanding

Dataset yang digunakan dalam proyek ini adalah **Wine Quality Dataset** yang tersedia di Kaggle, bersumber dari *UCI Machine Learning Repository*. Dataset ini dapat diakses melalui tautan berikut: [https://www.kaggle.com/datasets/yasserh/wine-quality-dataset/data](https://www.kaggle.com/datasets/yasserh/wine-quality-dataset/data).

Dataset ini berisi 12 fitur fisikokimia, dengan `quality` sebagai variabel target yang telah diubah menjadi dua kelas: `0` (low) dan `1` (high).

Berikut adalah daftar fitur pada dataset:

  * `fixed acidity`: Keasaman total dalam *wine* (g/dm³).
  * `volatile acidity`: Jumlah asam asetat dalam *wine* (g/dm³).
  * `citric acid`: Jumlah asam sitrat (g/dm³), yang memberikan rasa segar.
  * `residual sugar`: Gula yang tersisa setelah fermentasi (g/dm³), menentukan tingkat kemanisan.
  * `chlorides`: Jumlah garam dalam *wine* (g/dm³).
  * `free sulfur dioxide`: Bagian dari SO2 yang bebas.
  * `total sulfur dioxide`: Jumlah total SO2.
  * `density`: Kepadatan *wine* (g/cm³).
  * `pH`: Tingkat keasaman *wine*.
  * `sulphates`: Kalium sulfat (g/dm³), berperan sebagai pengawet.
  * `alcohol`: Kandungan alkohol dalam *wine* (%).
  * `quality`: Kualitas *wine* yang telah diklasifikasikan menjadi low (0) atau high (1).

---

### Data Preparation
Pada tahap ini, beberapa teknik persiapan data dilakukan untuk memastikan data siap digunakan dalam pemodelan:

1.  **Penanganan *Outlier***: Nilai-nilai ekstrem (`volatile acidity`, `residual sugar`, `chlorides`, `free sulfur dioxide`, `total sulfur dioxide`, dan `sulphates`) diatasi menggunakan teknik **Winsorizing**. Teknik ini mengganti nilai yang berada di luar batas tertentu (misalnya, persentil 5 dan 95) dengan nilai ambang batas tersebut.
2.  **Penanganan Redundansi Fitur**: Dilakukan analisis korelasi antar fitur. Ditemukan korelasi yang sangat kuat antara `total sulfur dioxide_winsor` dan `free sulfur dioxide_winsor` ($r=0.69$). Untuk menghindari multikolinearitas dan menyederhanakan model, salah satu fitur (yaitu, `total sulfur dioxide_winsor`) dihapus. Meskipun `fixed acidity` dan `density` juga berkorelasi ($r=0.68$), keduanya dipertahankan karena uji coba menunjukkan penghapusan `density` justru menurunkan performa model.
3.  **Pembagian Data**: Data dibagi menjadi data pelatihan (*training*) dan data pengujian (*testing*) dengan rasio 80:20 untuk membangun dan mengevaluasi model.
4.  **Penanganan Ketidakseimbangan Kelas (*Imbalanced Data*)**: Karena distribusi kelas target (`low` dan `high`) tidak seimbang, teknik **SMOTE (*Synthetic Minority Over-sampling Technique*)** diterapkan pada data pelatihan. SMOTE menghasilkan sampel sintetis untuk kelas minoritas (`high`), sehingga jumlah sampel di setiap kelas menjadi seimbang.
5.  **Standarisasi Fitur**: Menggunakan `RobustScaler` untuk menstandarisasi fitur-fitur numerik pada data. Proses ini penting untuk model yang sensitif terhadap skala fitur, seperti `Logistic Regression` dan `SVM`, sehingga semua fitur memiliki rentang nilai yang seragam.

-----

### Modeling
Pada tahap ini, lima model klasifikasi *machine learning* diterapkan untuk menyelesaikan masalah yang telah didefinisikan.

**Model yang Digunakan:**

  * `Logistic Regression`: Model linier dasar untuk klasifikasi.
  * `Random Forest`: Model *ensemble* berbasis pohon keputusan yang membangun banyak pohon secara paralel.
  * `XGBoost` & `Gradient Boosting`: Model *ensemble* yang membangun pohon keputusan secara sekuensial, dengan setiap pohon baru berusaha memperbaiki kesalahan dari pohon sebelumnya.
  * `SVM` (Support Vector Machine): Model yang mencari *hyperplane* optimal untuk memisahkan kelas.
  * `KNN` (K-Nearest Neighbors): Model non-parametrik yang mengklasifikasikan data berdasarkan mayoritas kelas dari tetangga terdekatnya.

Berdasarkan perbandingan awal, **Random Forest** menunjukkan performa terbaik. Oleh karena itu, Random Forest dipilih sebagai model utama untuk dioptimalkan melalui *hyperparameter tuning*.

**Proses *Improvement*:**
`Hyperparameter tuning` pada **Random Forest** dilakukan untuk menemukan kombinasi parameter terbaik yang dapat meningkatkan performa model. Parameter yang akan dioptimalkan antara lain:

  * `n_estimators`: Jumlah pohon dalam *forest*.
  * `max_depth`: Kedalaman maksimum setiap pohon.
  * `max_features`: Jumlah fitur yang dipertimbangkan untuk setiap *split*.

-----

### Evaluation

Metrik evaluasi yang digunakan untuk mengukur performa model adalah **Akurasi, Precision, Recall,** dan **F1-Score**.

  * **Akurasi**: Persentase prediksi yang benar dari total prediksi.
  * **Precision**: Kemampuan model untuk tidak mengklasifikasikan sampel negatif sebagai positif.
  * **Recall**: Kemampuan model untuk menemukan semua sampel positif.
  * **F1-Score**: Rata-rata harmonik dari *precision* dan *recall*, memberikan keseimbangan antara keduanya.

**Hasil Proyek Berdasarkan Metrik Evaluasi:**

Berdasarkan hasil perbandingan model awal, `Random Forest` menunjukkan hasil terbaik dengan **skor akurasi sebesar 0.7991 (sekitar 80%)**.

Berikut adalah hasil evaluasi dari model `Random Forest`:

```
===== Random Forest =====
Score: 0.7991
              precision    recall  f1-score   support

           0       0.82      0.80      0.81       124
           1       0.77      0.80      0.79       105

    accuracy                           0.80       229
   macro avg       0.80      0.80      0.80       229
weighted avg       0.80      0.80      0.80       229

[[99 25]
 [21 84]]
```

  * **Akurasi (0.80)**: Menunjukkan bahwa 80% dari total prediksi model adalah benar.
  * **Precision & Recall**: Nilai *precision* dan *recall* yang seimbang antara kelas 0 dan 1 menunjukkan bahwa model tidak bias terhadap salah satu kelas.
  * **F1-Score**: Nilai `F1-Score` yang tinggi untuk kedua kelas (0.81 dan 0.79) membuktikan bahwa model memiliki keseimbangan yang baik antara kemampuan memprediksi positif dengan benar dan menemukan semua positif.

**Kesimpulan**: Model **Random Forest** memberikan performa terbaik dalam memprediksi kualitas *wine* dengan akurasi 80%. Hasil ini membuktikan bahwa pendekatan *machine learning* dapat menjadi solusi yang efektif untuk mengklasifikasikan kualitas *wine* secara objektif.

----

### Saran 
1. Eksplorasi Lanjutan: Pertimbangkan untuk mencoba model boosting seperti XGBoost dengan tuning yang lebih dalam dan eksplorasi feature engineering.
2. Implementasi: Setelah model final didapat, model tersebut dapat di-deploy untuk membantu produsen wine dalam mengontrol kualitas produk secara otomatis.

