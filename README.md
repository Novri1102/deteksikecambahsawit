# 🌱 Web Deteksi Kecambah Layak Salur — Pusat Penelitian Kelapa Sawit

Aplikasi web full-stack untuk mendeteksi kecambah kelapa sawit **LAYAK SALUR**
dan **TIDAK LAYAK SALUR** secara otomatis menggunakan model AI (YOLOv8 versi
TensorFlow Lite: `best.tflite`).

## Fitur
- Deteksi dari **upload gambar** (drag & drop / pilih file) maupun **kamera laptop**
- Bounding box + label + confidence otomatis digambar pada foto hasil
- Hitung otomatis: jumlah layak salur, tidak layak salur, dan persentase kelayakan
- **Riwayat deteksi tersimpan otomatis** di database SQLite (`kecambah.db`) — bisa dilihat di halaman "Riwayat"
- Slider Confidence Threshold & IoU Threshold agar sensitivitas deteksi bisa disesuaikan
- Tampilan web responsif, siap dipakai di laptop/PC

## Arsitektur
```
kecambah-detector/
├── app.py              # Server Flask (backend, routing, API)
├── inference.py         # Modul AI: load model tflite, preprocessing, deteksi, NMS
├── database.py           # Modul SQLite untuk riwayat deteksi
├── best.tflite            # Model AI (YOLOv8, 2 kelas)
├── requirements.txt        # Daftar library Python yang dibutuhkan
├── kecambah.db              # (otomatis dibuat) database riwayat deteksi
├── templates/
│   ├── base.html            # Layout dasar (navbar, footer)
│   ├── index.html            # Halaman utama (upload/kamera + hasil)
│   └── history.html           # Halaman riwayat deteksi
└── static/
    ├── css/style.css          # Styling
    ├── js/main.js               # Logika frontend (upload, kamera, panggil API)
    ├── uploads/                  # (folder cadangan)
    └── annotated/                  # Gambar hasil deteksi tersimpan di sini
```

---

## 🔧 LANGKAH-LANGKAH INSTALASI (100% siap pakai)

### 1. Persiapan Awal
Pastikan laptop Anda sudah terinstal:
- **Python 3.9 – 3.12** (cek dengan `python --version` di terminal/CMD).
  Jika belum ada, unduh di https://www.python.org/downloads/ (saat instalasi,
  centang **"Add Python to PATH"**).
- **Visual Studio Code** — https://code.visualstudio.com/
- Ekstensi **Python** dari Microsoft di VSCode (buka tab Extensions, cari "Python", install).

### 2. Salin Folder Proyek
Salin seluruh folder `kecambah-detector` (yang berisi `app.py`, `best.tflite`, dll)
ke laptop Anda, misalnya ke `D:\kecambah-detector` atau `~/kecambah-detector`.

### 3. Buka Folder di VSCode
- Buka VSCode → **File → Open Folder** → pilih folder `kecambah-detector`.
- Buka terminal bawaan VSCode: **Terminal → New Terminal**.

### 4. Buat Virtual Environment (disarankan, agar rapi)
Di terminal VSCode, jalankan:

**Windows:**
```bash
python -m venv venv
venv\Scripts\activate
```

**Mac/Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
```

Setelah aktif, di depan baris terminal akan muncul tulisan `(venv)`.

> Di VSCode, setelah venv dibuat, biasanya akan muncul notifikasi
> "Select Interpreter" — pilih interpreter yang mengarah ke `venv`.

### 5. Install Semua Library yang Dibutuhkan
Masih di terminal yang sama (dengan `(venv)` aktif), jalankan:
```bash
pip install -r requirements.txt
```
Tunggu sampai proses selesai (proses ini mengunduh TensorFlow, Flask, OpenCV, dsb —
tergantung koneksi internet, bisa memakan waktu beberapa menit).

### 6. Jalankan Aplikasi
```bash
python app.py
```
Jika berhasil, di terminal akan muncul tulisan:
```
Buka browser: http://127.0.0.1:5000
```

### 7. Buka di Browser
Buka browser (Chrome/Edge/Firefox), lalu kunjungi:
```
http://127.0.0.1:5000
```
Aplikasi web siap digunakan! 🎉

---

## 📸 Cara Menggunakan Aplikasi

1. **Tab "Unggah Gambar"** — klik kotak putus-putus atau seret foto kecambah ke sana.
   **Tab "Kamera"** — klik "Aktifkan Kamera", lalu "Ambil Foto" untuk memotret langsung.
2. Atur **Confidence Threshold** (semakin tinggi = semakin ketat/yakin sebelum dianggap terdeteksi)
   dan **IoU Threshold** (mengatur toleransi tumpang-tindih kotak deteksi) sesuai kebutuhan.
   Nilai default (0.35 dan 0.45) sudah cukup baik untuk kebanyakan kasus.
3. Klik **"🔍 Jalankan Deteksi"**.
4. Hasil akan muncul: foto dengan kotak deteksi (hijau = Layak Salur, merah = Tidak Layak Salur),
   beserta jumlah dan persentase.
5. Klik **"⬇ Unduh Gambar Hasil"** untuk menyimpan gambar hasil deteksi.
6. Buka menu **"Riwayat"** di navbar untuk melihat seluruh histori deteksi yang tersimpan
   di database, lengkap dengan statistik total.

---

## ❓ Troubleshooting (Jika Ada Kendala)

**1. `pip install -r requirements.txt` gagal / lama sekali**
   - Pastikan koneksi internet stabil.
   - Coba jalankan: `pip install --upgrade pip` lalu ulangi.
   - Jika TensorFlow gagal terinstal karena Python versi terlalu baru (misal 3.13+),
     turunkan ke Python 3.11 atau 3.12.

**2. Error `ModuleNotFoundError`**
   - Pastikan virtual environment `(venv)` sedang aktif sebelum `python app.py`.
   - Jalankan ulang `pip install -r requirements.txt`.

**3. Kamera tidak bisa diakses di browser**
   - Browser modern mewajibkan koneksi aman (https) atau `localhost` untuk akses kamera.
     Karena kita mengakses lewat `http://127.0.0.1:5000` (localhost), seharusnya sudah aman.
   - Pastikan Anda mengizinkan (allow) permintaan izin kamera dari browser.

**4. Port 5000 sudah dipakai aplikasi lain**
   - Edit baris terakhir di `app.py`: ganti `port=5000` menjadi misalnya `port=5050`,
     lalu akses `http://127.0.0.1:5050`.

**5. Hasil deteksi kurang akurat**
   - Gunakan foto dengan pencahayaan cukup dan kecambah terlihat jelas, tidak blur.
   - Coba turunkan Confidence Threshold jika kecambah yang jelas terlihat tidak terdeteksi.
   - Coba naikkan Confidence Threshold jika terlalu banyak kotak yang salah/berlebih.
   - Akurasi akhir bergantung pada kualitas model `best.tflite` (hasil training) itu sendiri.

---

## ℹ️ Catatan Teknis
- Model `best.tflite` adalah model **YOLOv8 (Ultralytics)** hasil training khusus deteksi
  kecambah kelapa sawit, dengan 2 kelas: `LAYAK SALUR` dan `TIDAK LAYAK SALUR`.
- Model ini **bukan database**, melainkan model kecerdasan buatan (AI) untuk mendeteksi
  objek pada gambar. Untuk kebutuhan penyimpanan data hasil deteksi, aplikasi ini
  sudah dilengkapi database sungguhan (`kecambah.db`, format SQLite) yang otomatis
  mencatat setiap sesi deteksi (waktu, jumlah layak/tidak layak, gambar hasil).
- Semua data (`kecambah.db` dan folder `static/annotated`) tersimpan lokal di laptop Anda,
  tidak dikirim ke server manapun.
