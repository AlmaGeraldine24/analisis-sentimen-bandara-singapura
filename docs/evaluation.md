# Hasil Eksperimen & Evaluasi Model

Proyek ini menguji 3 skema eksperimen dengan algoritma berbeda dan pembagian data *train/test* yang bervariasi untuk menemukan model terbaik.

---

## 3.1 Skema Eksperimen

| Skema | Algoritma | Pembagian Data | Akurasi Akhir |
| :--- | :--- | :--- | :--- |
| **Scheme 1** | LinearSVC (dengan `class_weight='balanced'`) | 80% Train / 20% Test | **91.60%** |
| **Scheme 2** | **Logistic Regression (dengan `class_weight='balanced'`)** | 80% Train / 20% Test | **91.35% – 91.75%** |
| **Scheme 3** | RandomForest Classifier | 70% Train / 30% Test | **86.84%** |

---

## 3.2 Analisis Per Skema

### Scheme 1: LinearSVC

| Metrik | Positive | Neutral | Negative |
| :--- | :--- | :--- | :--- |
| **Precision** | 0.89 | 0.92 | 0.85 |
| **Recall** | 0.88 | 0.94 | 0.82 |
| **F1-Score** | 0.88 | 0.93 | 0.83 |

**Kelebihan:** Performa stabil di semua kelas.
**Kekurangan:** Sedikit lebih rendah di kelas negatif.

---

### Scheme 2: Logistic Regression (Best Model)

| Metrik | Positive | Neutral | Negative |
| :--- | :--- | :--- | :--- |
| **Precision** | 0.92 | 0.93 | 0.88 |
| **Recall** | 0.90 | 0.95 | 0.85 |
| **F1-Score** | 0.91 | 0.94 | 0.86 |

**Kelebihan:**
- Akurasi tertinggi (91.75%).
- Paling stabil dan seimbang (*balanced*).
- Terbaik dalam menangani *class imbalance*.

**Kekurangan:** - (tidak ada signifikan)

---

### Scheme 3: RandomForest

| Metrik | Positive | Neutral | Negative |
| :--- | :--- | :--- | :--- |
| **Precision** | 0.84 | 0.88 | 0.78 |
| **Recall** | 0.82 | 0.89 | 0.75 |
| **F1-Score** | 0.83 | 0.88 | 0.76 |

**Kelebihan:** Cepat dilatih.
**Kekurangan:** Akurasi terendah (86.84%), overfitting pada data mayoritas.

---

## 3.3 Artefak Output

Setelah menjalankan seluruh *pipeline*, file-file berikut akan tersimpan secara otomatis:

| File | Deskripsi |
| :--- | :--- |
| **`data_bandara_singapura_labeled_full.csv`** | Dataset final 9.993 baris dengan `text_clean` dan `label` |
| **`metrics_report.txt`** | Laporan lengkap precision, recall, f1-score per kelas |
| **`cm_scheme1.png`** | Confusion Matrix Scheme 1 (LinearSVC) |
| **`cm_scheme2.png`** | Confusion Matrix Scheme 2 (Logistic Regression) |
| **`cm_scheme3.png`** | Confusion Matrix Scheme 3 (RandomForest) |

---

## 3.4 Kesimpulan Akhir

> 🏆 **Model Terbaik:** Logistic Regression (Scheme 2)
> 📊 **Akurasi:** 91.75%
> ✅ **Alasan:** Akurasi tertinggi, stabil, dan paling seimbang dalam menangani ketimpangan data antar kelas (*class imbalance*).

**Model ini direkomendasikan untuk digunakan dalam produksi atau deployment.**

---

## 3.5 Saran Pengembangan ke Depan

| Aspek | Saran |
| :--- | :--- |
| **Data** | Tambahkan lebih banyak data negatif untuk menyeimbangkan kelas |
| **Fitur** | Eksperimen dengan embedding (Word2Vec, BERT) untuk peningkatan akurasi |
| **Model** | Uji coba dengan deep learning (LSTM, Transformer) |
| **Deployment** | Buat API sederhana menggunakan FastAPI atau Flask |

---

**Kembali ke [README →](../README.md)**
