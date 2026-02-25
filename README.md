# 🏍️ Sistem Database Bengkel Motor (MySQL)

> Project Database Relasional menggunakan **MySQL 8.4.3**
> Studi kasus: Manajemen Data Bengkel Motor

---

## 👥 Anggota Kelompok

1. **Geraldy Febriansyah**
2. **Naufal Imania**
3. **Aulia Novi**

🔗 LinkedIn (Project Owner):
[https://www.linkedin.com/in/geraldy-febriansyah-b850473a6](https://www.linkedin.com/in/geraldy-febriansyah-b850473a6)

---

# 📌 Deskripsi Project

Project ini bertujuan untuk membangun sistem database bengkel motor dengan konsep:

* ✅ Relational Database
* ✅ Primary Key & Foreign Key
* ✅ JOIN (INNER, LEFT, RIGHT)
* ✅ UNION (Simulasi FULL JOIN)
* ✅ VIEW
* ✅ Filtering, Sorting, Aggregation

Struktur database terdiri dari:

* 👤 Pelanggan
* 🏍️ Motor
* 🔧 Servis

---

# 🗄️ 1. Setup Database

```sql
CREATE DATABASE db_bengkel_motor;
USE db_bengkel_motor;
```

---

# 📊 2. Struktur Tabel

---

## 👤 Tabel `pelanggan`

```sql
CREATE TABLE pelanggan (
    id_pelanggan INT PRIMARY KEY AUTO_INCREMENT,
    nama_pelanggan VARCHAR(100),
    alamat TEXT
);

ALTER TABLE pelanggan
ADD COLUMN no_telp VARCHAR(15);
```

### Struktur Final

| Field          | Tipe Data    | Keterangan        |
| -------------- | ------------ | ----------------- |
| id_pelanggan   | INT (PK, AI) | ID unik pelanggan |
| nama_pelanggan | VARCHAR(100) | Nama pelanggan    |
| alamat         | TEXT         | Alamat pelanggan  |
| no_telp        | VARCHAR(15)  | Nomor telepon     |

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

### Struktur

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

ALTER TABLE servis
ADD COLUMN status_pembayaran VARCHAR(20) DEFAULT 'belum lunas';
```

### Struktur

| Field             | Tipe Data     | Keterangan           |
| ----------------- | ------------- | -------------------- |
| id_servis         | INT (PK, AI)  | ID servis            |
| id_motor          | INT (FK)      | Relasi ke motor      |
| tanggal_servis    | DATE          | Tanggal servis       |
| keluhan           | TEXT          | Keluhan              |
| biaya             | DECIMAL(10,2) | Biaya                |
| status_pembayaran | VARCHAR(20)   | Default: belum lunas |

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

## 💰 Update Status

```sql
UPDATE servis
SET status_pembayaran = 'lunas'
WHERE id_servis IN (1,2,3,5);
```

---

# 🔎 4. Query Relasional

---

## 🔗 INNER JOIN

```sql
SELECT p.nama_pelanggan, m.plat_nomor, s.keluhan, s.biaya
FROM servis s
INNER JOIN motor m ON s.id_motor = m.id_motor
INNER JOIN pelanggan p ON m.id_pelanggan = p.id_pelanggan;
```

---

## ⬅️ LEFT JOIN

```sql
SELECT p.nama_pelanggan, m.plat_nomor
FROM pelanggan p
LEFT JOIN motor m ON p.id_pelanggan = m.id_pelanggan;
```

---

## ➡️ RIGHT JOIN

```sql
SELECT p.nama_pelanggan, m.plat_nomor
FROM pelanggan p
RIGHT JOIN motor m ON p.id_pelanggan = m.id_pelanggan;
```

---

## 🔄 Simulasi FULL JOIN

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

# 📈 5. Query Analisis

## 🔢 Hitung Motor Honda

```sql
SELECT COUNT(*) AS total_honda
FROM motor
WHERE merk = 'honda';
```

---

## 🔎 Filter + Sorting + Limit

```sql
SELECT *
FROM servis
WHERE keluhan = 'ganti oli' OR biaya > 1000000
ORDER BY biaya DESC
LIMIT 2;
```

---

# 📊 6. View Laporan

```sql
CREATE VIEW laporan_pendapatan AS
SELECT
    tanggal_servis,
    keluhan,
    biaya,
    status_pembayaran
FROM servis;
```

```sql
SELECT * FROM laporan_pendapatan;
```

---

# 🧠 Relasi Database

| Relasi            | Penjelasan                          |
| ----------------- | ----------------------------------- |
| Pelanggan → Motor | 1 pelanggan bisa punya banyak motor |
| Motor → Servis    | 1 motor bisa memiliki banyak servis |
| Foreign Key       | Menjaga integritas data             |

---

# 🎯 Kesimpulan

Project ini berhasil mengimplementasikan:

* ✔️ Database relasional
* ✔️ Integrity constraint
* ✔️ Query join kompleks
* ✔️ View laporan
* ✔️ Filtering & aggregation

Database siap dikembangkan ke:

* PHP Native
* Laravel
* Node.js
* REST API Backend

---

# 🚀 Status Project

✅ Struktur selesai
✅ Data terisi
✅ Query berjalan
✅ Siap untuk presentasi & portfolio

---