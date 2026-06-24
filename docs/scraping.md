
---

## 📁 File 2: `docs/scraping.md`

```markdown
# Tahap 1: Data Scraping — YouTube

Notebook `File_kode_scraping.ipynb` bertanggung jawab untuk mengumpulkan data komentar dari YouTube menggunakan **YouTube Data API v3**.

---

## 1.1 Inisialisasi API & Parameter

- Kode melakukan autentikasi menggunakan `API_KEY` yang disimpan sebagai variabel.
- Menentukan batas pencarian video (`max_results`) dan target jumlah komentar unik yang ingin dikumpulkan, misalnya `TARGET_COUNT = 10000`.
- Parameter ini memastikan bahwa scraping berhenti setelah mencapai target, menghindari pengambilan data berlebihan.

---

## 1.2 Pengacakan Kata Kunci (Query Bank)

Untuk menghindari bias topik dan mendapatkan variasi komentar, disiapkan sebuah `QUERY_BANK` yang berisi 15 variasi kata kunci, seperti:

- `"Singapore Airlines review"`
- `"Changi Airport transit"`
- `"Singapore Airlines economy class"`
- `"Singapore Airlines first class"`
- `"Singapore Airlines business class"`
- `"Singapore Airlines lounge"`
- `"Singapore Airlines food"`
- `"Singapore Airlines delay"`
- `"Singapore Airlines service"`
- `"Singapore Airlines safety"`
- `"Singapore Airlines Changi experience"`
- `"Singapore Airlines baggage"`
- `"Singapore Airlines customer service"`
- `"Singapore Airlines flight experience"`
- `"Singapore Airlines comfort"`

**Mekanisme:**
- Fungsi `random.shuffle()` mengacak urutan kata kunci setiap kali proses pencarian dimulai.
- Hal ini memastikan hasil pencarian tidak selalu didominasi oleh satu kata kunci tertentu.

---

## 1.3 Pencarian Video & Mapping Metadata

**Proses:**
1. Menggunakan pustaka `googleapiclient.discovery.build` untuk membuat objek `youtube` yang terhubung dengan API.
2. Untuk setiap kata kunci dalam `QUERY_BANK`, dilakukan pencarian video dengan parameter:
   - `part='snippet'`
   - `maxResults=50` (nilai maksimum per permintaan)
   - `order='relevance'`
   - `publishedAfter` untuk membatasi video dalam rentang waktu tertentu.

**Hasil:**
- Pencarian menghasilkan daftar `video_id` yang dikumpulkan dan dipetakan bersama metadatanya:
  - `video_title` (judul video)
  - `channel_id` (ID channel)
  - `channel_title` (nama channel)
  - `published_at` (tanggal publikasi video)

**Anti-Duplikat:**
- Video yang sama tidak akan diproses ulang karena disimpan dalam sebuah `set` untuk mencegah duplikasi.

---

## 1.4 Penarikan Komentar Utama & Balasan

**Proses Utama:**
- Berdasarkan daftar video yang terkumpul, kode melakukan iterasi untuk mengambil komentar menggunakan `youtube.commentThreads().list()` dengan parameter:
  - `part='snippet,replies'` (untuk mendapatkan komentar utama dan balasan)
  - `maxResults=100` (maksimum komentar per permintaan)
  - `videoId` dari setiap video.

**Data yang Diambil per Komentar:**
- `comment_id`
- `text_display` (isi komentar)
- `author_display_name`
- `published_at`
- `like_count`

**Penarikan Balasan (Replies):**
- Jika sebuah komentar memiliki balasan (`replies`), kode akan melakukan *looping* tambahan untuk mengambil setiap balasan menggunakan `youtube.comments().list()` dengan `parentId` dari komentar utama.
- Semua teks (komentar utama dan balasan) disimpan dalam satu list untuk diproses lebih lanjut.

---

## 1.5 Filter Bahasa Inggris & Eliminasi Duplikat

**Pembersihan Awal:**
- Setiap teks komentar diperiksa dengan pustaka `langdetect` untuk memastikan teks tersebut berbahasa Inggris.
- Komentar dalam bahasa lain otomatis diabaikan.

**Filter Karakter:**
- Kode memeriksa rasio karakter ASCII dalam teks untuk menyaring komentar yang hanya berisi emoji berlebihan atau karakter non-teks.

**Anti-Duplikat:**
- Setiap `comment_id` yang berhasil diambil diperiksa ke dalam sebuah `set` Python.
- Jika ID sudah ada, komentar dilewati.
- Proses ini memastikan bahwa 10.000 data yang terkumpul benar-benar unik.

---

## 1.6 Penyimpanan Data Mentah

**Proses:**
1. Setelah target 10.000 komentar tercapai atau pencarian selesai, semua data dikonversi menjadi Pandas DataFrame.
2. DataFrame memiliki kolom:
   - `comment_id`
   - `video_id`
   - `video_title`
   - `channel_id`
   - `channel_title`
   - `published_at`
   - `author_display_name`
   - `text_display`
   - `like_count`

**Output:**
- Data diekspor ke file CSV mentah bernama **`data_bandara_singapura.csv`** sebagai bahan baku untuk tahap pelabelan dan pelatihan model.

---

## 📊 Statistik Awal

| Metrik | Nilai |
| :--- | :--- |
| **Total Komentar Terambil** | 10.000 |
| **Total Video Diproses** | Variatif (tergantung pencarian) |
| **Format Output** | CSV |
| **Bahasa** | Inggris |

---

**Lanjut ke [Tahap 2: Pelatihan Model →](training.md)**
