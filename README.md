----------

# 🛵 dokumentasi project: database sistem bengkel motor

project ini dirancang untuk mengelola operasional bengkel motor secara digital. dokumentasi ini mencakup seluruh perintah sql yang diperlukan dari tahap perancangan hingga pelaporan.

----------

## 🏗️ 1. data definition language (ddl)

tahap ini bertujuan untuk membangun struktur database dan tabel.

SQL

```
-- 1. membuat database
create database db_bengkel_pro;
use db_bengkel_pro;

-- 2. membuat tabel pelanggan (master data)
create table pelanggan (
    id_pelanggan int primary key auto_increment,
    nama_pelanggan varchar(100) not null,
    no_telp varchar(15),
    alamat text
);

-- 3. membuat tabel motor (relasi ke pelanggan)
create table motor (
    id_motor int primary key auto_increment,
    plat_nomor varchar(15) unique not null,
    merk varchar(50),
    tipe varchar(50),
    id_pelanggan int,
    foreign key (id_pelanggan) references pelanggan(id_pelanggan)
);

-- 4. membuat tabel servis (relasi ke motor)
create table servis (
    id_servis int primary key auto_increment,
    id_motor int,
    tanggal_servis date,
    keluhan text,
    biaya decimal(10,2),
    foreign key (id_motor) references motor(id_motor)
);

-- 5. alter: memodifikasi tabel untuk menambah kolom status
alter table servis add column status_pembayaran varchar(20) default 'belum lunas';

```

> **penjelasan**: tabel dibuat berurutan dari pelanggan ke servis agar **foreign key** (kunci penghubung) bisa merujuk ke tabel induk yang sudah ada.

----------

## 📥 2. data manipulation language (dml)

tahap pengisian data minimal 5 baris dan pembaruan data.

SQL

```
-- mengisi data pelanggan
insert into pelanggan (nama_pelanggan, no_telp, alamat) values
('andi setiawan', '081122', 'jakarta'),
('geraldy', '082233', 'bandung'),
('novi', '083344', 'surabaya'),
('dewi puspa', '084455', 'malang'),
('opang', '085566', 'semarang'),
('fajar sidik', '086677', 'medan'); -- pelanggan ke-6 untuk tes join

-- mengisi data motor (id_pelanggan merujuk ke tabel pelanggan)
insert into motor (plat_nomor, merk, tipe, id_pelanggan) values
('b 1234 aaa', 'honda', 'vario', 1),
('d 5678 bbb', 'yamaha', 'nmax', 2),
('l 9012 ccc', 'suzuki', 'gsx', 3),
('n 3456 ddd', 'kawasaki', 'ninja', 4),
('h 7890 eee', 'honda', 'beat', 5),
('bk 111 fff', 'yamaha', 'mio', null); -- motor tanpa pemilik untuk tes join

-- mengisi data servis
insert into servis (id_motor, tanggal_servis, keluhan, biaya) values
(1, '2026-02-10', 'ganti oli', 150000),
(2, '2026-02-11', 'servis rem', 75000),
(3, '2026-02-12', 'ganti ban', 450000),
(4, '2026-02-13', 'turun mesin', 1200000),
(5, '2026-02-14', 'servis rutin', 200000);

-- update: mengubah data yang sudah ada
update servis set status_pembayaran = 'lunas' where id_servis = 1;
update pelanggan set alamat = 'bandung kota' where nama_pelanggan = 'geraldy';

```

----------

## 🔍 3. analisis data & join

menghubungkan tabel-tabel untuk menghasilkan informasi yang berguna.

### a. kueri gabungan (tanpa alias)

SQL

```
-- laporan lengkap pelanggan, motor, dan keluhan servisnya
select 
    pelanggan.nama_pelanggan, 
    motor.plat_nomor, 
    servis.tanggal_servis, 
    servis.keluhan, 
    servis.biaya
from pelanggan
join motor on pelanggan.id_pelanggan = motor.id_pelanggan
join servis on motor.id_motor = servis.id_motor;

```

### b. variasi join

-   **left join**: melihat semua pelanggan, termasuk yang belum punya motor.
    
    SQL
    
    ```
    select pelanggan.nama_pelanggan, motor.plat_nomor 
    from pelanggan 
    left join motor on pelanggan.id_pelanggan = motor.id_pelanggan;
    
    ```
    
-   **right join**: melihat semua motor, termasuk yang data pemiliknya belum terdaftar.
    
    SQL
    
    ```
    select pelanggan.nama_pelanggan, motor.plat_nomor 
    from pelanggan 
    right join motor on pelanggan.id_pelanggan = motor.id_pelanggan;
    
    ```
    
-   **full join (simulasi)**: menampilkan seluruh data dari kedua sisi.
    
    SQL
    
    ```
    select pelanggan.nama_pelanggan, motor.plat_nomor from pelanggan left join motor on pelanggan.id_pelanggan = motor.id_pelanggan
    union
    select pelanggan.nama_pelanggan, motor.plat_nomor from pelanggan right join motor on pelanggan.id_pelanggan = motor.id_pelanggan;
    
    ```
    

----------

## 📈 4. fungsi agregasi & filter

SQL

```
-- count: menghitung total transaksi servis
select count(*) as total_transaksi from servis;

-- or: mencari motor merk honda atau suzuki
select * from motor where merk = 'honda' or merk = 'suzuki';

-- limit: menampilkan 3 transaksi servis dengan biaya tertinggi
select * from servis order by biaya desc limit 3;

```

----------

## 🖼️ 5. view (laporan otomatis)

view mempermudah pemanggilan data yang kompleks menjadi satu perintah sederhana.

SQL

```
-- membuat view untuk laporan harian
create view laporan_harian_bengkel as
select 
    servis.tanggal_servis, 
    pelanggan.nama_pelanggan, 
    motor.plat_nomor, 
    servis.keluhan, 
    servis.biaya,
    servis.status_pembayaran
from servis
join motor on servis.id_motor = motor.id_motor
join pelanggan on motor.id_pelanggan = pelanggan.id_pelanggan;

-- cara memanggil laporan
select * from laporan_harian_bengkel;

```

----------

### 💡 kesimpulan materi

1.  **ddl**: membuat kerangka rumah (database & tabel).
    
2.  **dml**: mengisi penghuni rumah (data pelanggan & motor).
    
3.  **join**: menghubungkan antar ruangan (relasi antar tabel).
    
4.  **view**: membuat jendela pantau (laporan praktis).
    
5.  **alias (optional)**: nama panggilan singkat (seperti `p.` untuk `pelanggan`) untuk mempercepat penulisan.