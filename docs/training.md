# Tahap 2: Pelatihan Model

Notebook `Notebook_pelatihan_model_.ipynb` melanjutkan proses dengan mengolah data mentah, melakukan pembersihan, pelabelan otomatis, ekstraksi fitur, hingga melatih dan mengevaluasi model *Machine Learning*.

---

## 2.1 Preprocessing Teks (Pembersihan)

Data mentah yang masih berupa teks komentar kotor (*noisy*) dibersihkan menggunakan fungsi kustom `clean_text()`.

**Langkah-langkah pembersihan:**

| Langkah | Deskripsi | Contoh |
| :--- | :--- | :--- |
| **Hapus URL** | Menghapus semua tautan (http, https, www) menggunakan regex | `"Check this https://t.co/abc"` → `"Check this"` |
| **Hapus Mention & Tagar** | Menghilangkan sebutan akun (`@username`) dan tagar (`#topic`) | `"@user thanks #awesome"` → `"thanks"` |
| **Hapus Entitas HTML** | Mengubah atau menghapus kode HTML seperti `&amp;`, `&lt;` | `"A&amp;B"` → `"A&B"` |
| **Lowercasing** | Mengubah semua huruf menjadi huruf kecil | `"GOOD"` → `"good"` |
| **Jaga Tanda Baca Penting** | Mempertahankan `!`, `?`, `,`, `.` untuk menjaga intonasi sentimen | `"good!"` vs `"good."` dibedakan |

**Hasil:** Data bersih siap untuk proses pelabelan.

---

## 2.2 Pelabelan Otomatis (Hybrid Auto-Label)

Karena data komentar tidak memiliki label sentimen, proyek ini menerapkan pendekatan **Domain Lexicon + Phrase Rules** untuk melabeli data secara otomatis menjadi 3 kelas:

| Kelas | Keterangan |
| :--- | :--- |
| **Positive** | Sentimen positif (puas, senang, merekomendasikan) |
| **Neutral** | Sentimen netral (faktual, informatif) |
| **Negative** | Sentimen negatif (kecewa, mengeluh, marah) |

---

### Mekanisme Pelabelan

**1. Phrase Boost (Frasa Khusus Penerbangan)**

Bobot besar diberikan pada frasa khas dunia penerbangan yang sangat indikatif:

| Frasa Positif | Bobot | Frasa Negatif | Bobot |
| :--- | :--- | :--- | :--- |
| `"best flight"` | +5 | `"lost luggage"` | -5 |
| `"excellent service"` | +5 | `"delayed"` | -4 |
| `"great experience"` | +4 | `"cancelled"` | -4 |
| `"comfortable seat"` | +3 | `"bad food"` | -3 |
| `"friendly crew"` | +3 | `"uncomfortable"` | -3 |

**2. Lexicon Scoring (Kamus Kata Umum)**

Menggunakan kamus kata positif dan negatif umum, kemudian menggabungkannya dengan:

- **Negator Detection:** Kata seperti `not`, `never`, `no` akan **membalikkan** arah sentimen kata berikutnya.
  - Contoh: `"not good"` → awalnya "good" (+1), ditambah "not" → menjadi (-1) → negatif.
  
- **Intensifier Detection:** Kata seperti `very`, `extremely`, `really` akan **melipatgandakan** bobot skor kata yang mengikutinya.
  - Contoh: `"very good"` → "good" (+1) × 2 (karena "very") → (+2) → sangat positif.

**3. Penentuan Label Akhir**

| Skor Akhir | Label |
| :--- | :--- |
| `> 0` | Positive |
| `== 0` | Neutral |
| `< 0` | Negative |

---

### Hasil Distribusi Label

| Label | Jumlah Data |
| :--- | :--- |
| **Neutral** | 8.357 |
| **Positive** | 1.488 |
| **Negative** | 148 |

> ⚠️ **Catatan:** Data menunjukkan ketidakseimbangan (*class imbalance*) yang signifikan. Hal ini akan ditangani pada saat pelatihan model menggunakan parameter `class_weight='balanced'`.

---

## 2.3 Ekstraksi Fitur (Hybrid TF-IDF)

Untuk mengubah teks bersih menjadi representasi numerik yang dapat diproses oleh algoritma *Machine Learning*, digunakan pendekatan **Hybrid TF-IDF Vectorizer**.

### Word-Level TF-IDF (N-gram 1–3)

- Menangkap urutan kata (unigram, bigram, trigram).
- Memahami konteks frasa utuh.
- Contoh: `"not good"` akan berbeda dengan `"good"` (karena bigram `"not good"` tertangkap).

### Char-Level TF-IDF (N-gram 3–5)

- Menangkap pola kombinasi karakter.
- Mengantisipasi variasi penulisan kata tidak baku, singkatan, atau salah ketik (*typo*).
- Contoh: `"excelent"` tetap dikenali karena kemiripan karakternya dengan `"excellent"`.

**Keuntungan Pendekatan Hybrid:**
- Word-level menangkap makna semantik.
- Char-level menangkap variasi morfologis.
- Kombinasi keduanya meningkatkan performa model.

---

## 📊 Hasil Akhir Pipeline

| Output | Deskripsi |
| :--- | :--- |
| **`data_bandara_singapura_labeled_full.csv`** | Dataset final dengan kolom `text_clean` dan `label` |
| **Jumlah Data** | 9.993 baris |
| **Kelas** | Positive, Neutral, Negative |

---

**Lanjut ke [Hasil Evaluasi →](evaluation.md)**
