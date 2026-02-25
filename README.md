# 🏍️ Sistem Database Bengkel Motor (MySQL)

Dokumentasi ini menjelaskan pembuatan dan pengelolaan database **Bengkel Motor** menggunakan **MySQL 8.4.3** secara terstruktur dan siap digunakan sebagai `README.md`.

---

## 📦 1. Membuat Database

```sql
CREATE DATABASE db_bengkel_motor;
USE db_bengkel_motor;
```

📌 Database ini digunakan untuk menyimpan data:

* Pelanggan
* Motor
* Servis

---

# 🗂️ 2. Struktur Tabel

---

## 👤 Tabel `pelanggan`

```sql
CREATE TABLE pelanggan (
    id_pelanggan INT PRIMARY KEY AUTO_INCREMENT,
    nama_pelanggan VARCHAR(100),
    alamat TEXT
);
```

### ➕ Penambahan Kolom

```sql
ALTER TABLE pelanggan
ADD COLUMN no_telp VARCHAR(15);
```

### 📊 Struktur Akhir

| Field          | Tipe Data    | Keterangan              |
| -------------- | ------------ | ----------------------- |
| id_pelanggan   | INT (PK, AI) | ID unik pelanggan       |
| nama_pelanggan | VARCHAR(100) | Nama pelanggan          |
| alamat         | TEXT         | Alamat pelanggan        |
| no_telp        | VARCHAR(15)  | Nomor telepon pelanggan |

---

## 🏍️ Tabel `motor`

```sql
CREATE TABLE motor (
    id_motor INT PRIMARY KEY AUTO_INCREMENT,
    plat_nomor VARCHAR(15) UNIQUE,
    merk VARCHAR(50),
    id_pelanggan INT,
    FOREIGN KEY (id_pelanggan) REFERENCES pelanggan(id_pelanggan)
);
```

### 📊 Struktur Tabel

| Field        | Tipe Data    | Keterangan          |
| ------------ | ------------ | ------------------- |
| id_motor     | INT (PK, AI) | ID motor            |
| plat_nomor   | VARCHAR(15)  | Plat nomor (unik)   |
| merk         | VARCHAR(50)  | Merk motor          |
| id_pelanggan | INT (FK)     | Relasi ke pelanggan |

---

## 🔧 Tabel `servis`

```sql
CREATE TABLE servis (
    id_servis INT PRIMARY KEY AUTO_INCREMENT,
    id_motor INT,
    tanggal_servis DATE,
    keluhan TEXT,
    biaya DECIMAL(10,2),
    FOREIGN KEY (id_motor) REFERENCES motor(id_motor)
);
```

### ➕ Tambah Kolom Status Pembayaran

```sql
ALTER TABLE servis
ADD COLUMN status_pembayaran VARCHAR(20) DEFAULT 'belum lunas';
```

### 📊 Struktur Tabel

| Field             | Tipe Data     | Keterangan                    |
| ----------------- | ------------- | ----------------------------- |
| id_servis         | INT (PK, AI)  | ID servis                     |
| id_motor          | INT (FK)      | Relasi ke motor               |
| tanggal_servis    | DATE          | Tanggal servis                |
| keluhan           | TEXT          | Keluhan pelanggan             |
| biaya             | DECIMAL(10,2) | Biaya servis                  |
| status_pembayaran | VARCHAR(20)   | Status (default: belum lunas) |

---

# 📝 3. Insert Data

## 👤 Data Pelanggan

```sql
INSERT INTO pelanggan (nama_pelanggan, no_telp, alamat) VALUES
('andi setiawan', '0811', 'jakarta'),
('geraldy', '0822', 'bandung'),
('novi', '0833', 'surabaya'),
('dewi puspa', '0844', 'malang'),
('opang', '0855', 'semarang'),
('fajar sidik', '0866', 'medan');
```

---

## 🏍️ Data Motor

```sql
INSERT INTO motor (plat_nomor, merk, id_pelanggan) VALUES
('b 1234 aaa', 'honda', 1),
('d 5678 bbb', 'yamaha', 2),
('l 9012 ccc', 'suzuki', 3),
('n 3456 ddd', 'kawasaki', 4),
('h 7890 eee', 'honda', 5),
('bk 111 fff', 'yamaha', NULL);
```

---

## 🔧 Data Servis

```sql
INSERT INTO servis (id_motor, tanggal_servis, keluhan, biaya) VALUES
(1, '2026-02-01', 'ganti oli', 150000),
(2, '2026-02-02', 'servis rem', 200000),
(3, '2026-02-03', 'ganti ban', 450000),
(4, '2026-02-04', 'turun mesin', 1500000),
(5, '2026-02-05', 'servis rutin', 250000);
```

---

## 💰 Update Status Pembayaran

```sql
UPDATE servis
SET status_pembayaran = 'lunas'
WHERE id_servis IN (1,2,3,5);
```

---

# 🔎 4. Query & Join

---

## 🔗 INNER JOIN

Menampilkan data servis lengkap dengan nama pelanggan.

```sql
SELECT p.nama_pelanggan, m.plat_nomor, s.keluhan, s.biaya
FROM servis s
INNER JOIN motor m ON s.id_motor = m.id_motor
INNER JOIN pelanggan p ON m.id_pelanggan = p.id_pelanggan;
```

### 📊 Hasil

| Nama Pelanggan | Plat Nomor | Keluhan      | Biaya   |
| -------------- | ---------- | ------------ | ------- |
| andi setiawan  | b 1234 aaa | ganti oli    | 150000  |
| geraldy        | d 5678 bbb | servis rem   | 200000  |
| novi           | l 9012 ccc | ganti ban    | 450000  |
| dewi puspa     | n 3456 ddd | turun mesin  | 1500000 |
| opang          | h 7890 eee | servis rutin | 250000  |

---

## ⬅️ LEFT JOIN

Menampilkan semua pelanggan meskipun belum punya motor.

```sql
SELECT p.nama_pelanggan, m.plat_nomor
FROM pelanggan p
LEFT JOIN motor m ON p.id_pelanggan = m.id_pelanggan;
```

---

## ➡️ RIGHT JOIN

Menampilkan semua motor meskipun belum punya pemilik.

```sql
SELECT p.nama_pelanggan, m.plat_nomor
FROM pelanggan p
RIGHT JOIN motor m ON p.id_pelanggan = m.id_pelanggan;
```

---

## 🔄 FULL OUTER JOIN (Simulasi)

MySQL tidak mendukung FULL JOIN langsung, jadi gunakan UNION:

```sql
SELECT p.nama_pelanggan, m.plat_nomor
FROM pelanggan p
LEFT JOIN motor m ON p.id_pelanggan = m.id_pelanggan
UNION
SELECT p.nama_pelanggan, m.plat_nomor
FROM pelanggan p
RIGHT JOIN motor m ON p.id_pelanggan = m.id_pelanggan;
```

---

# 📊 5. Query Tambahan

---

## 🔢 Menghitung Jumlah Motor Honda

```sql
SELECT COUNT(*) AS total_honda
FROM motor
WHERE merk = 'honda';
```

---

## 🔎 Filter + ORDER + LIMIT

```sql
SELECT *
FROM servis
WHERE keluhan = 'ganti oli' OR biaya > 1000000
ORDER BY biaya DESC
LIMIT 2;
```

---

# 📈 6. VIEW (Virtual Table)

## 📊 Membuat View Laporan Pendapatan

```sql
CREATE VIEW laporan_pendapatan AS
SELECT
    tanggal_servis,
    keluhan,
    biaya,
    status_pembayaran
FROM servis;
```

### 🔎 Mengakses View

```sql
SELECT * FROM laporan_pendapatan;
```

📌 View mempermudah pembuatan laporan tanpa menulis ulang query kompleks.

---

# 🧠 Konsep Relasi Database

| Relasi            | Penjelasan                                  |
| ----------------- | ------------------------------------------- |
| Pelanggan → Motor | 1 pelanggan bisa punya banyak motor         |
| Motor → Servis    | 1 motor bisa memiliki banyak riwayat servis |
| Foreign Key       | Menjaga integritas data antar tabel         |

---

# 🎯 Kesimpulan

✅ Menggunakan PRIMARY KEY & FOREIGN KEY
✅ Menggunakan JOIN untuk relasi data
✅ Menggunakan VIEW untuk laporan
✅ Menggunakan DEFAULT VALUE
✅ Menggunakan UNION untuk simulasi FULL JOIN

---

# 🚀 Status Proyek

Database bengkel motor berhasil dibuat dengan:

* Struktur relasional yang benar
* Integritas data terjaga
* Query analisis berjalan dengan baik
* Siap dikembangkan ke aplikasi berbasis web (PHP / Laravel / Node.js)

---

Kalau kamu mau, saya bisa tambahkan:

* ERD Diagram
* Trigger otomatis ubah status pembayaran
* Stored Procedure laporan bulanan
* Versi lebih profesional untuk portfolio GitHub

Tinggal bilang 🔥
