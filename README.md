# Sistem-perdataan-bengkel-motor
bengkel motor kelompok 7
🛠️ project database: sistem manajemen bengkel motor 🏍️
dokumentasi ini mencakup seluruh alur kerja database relasional menggunakan mysql, mulai dari tahap pembangunan hingga pelaporan otomatis.

⚙️ 1. tahap ddl: membangun struktur tabel
tahap ini adalah mendesain kerangka database. kita membuat database, tabel, serta menentukan hubungan antar tabel tersebut.

SQL
create database db_bengkel;
use db_bengkel;

create table pelanggan (
    id_pelanggan int primary key auto_increment,
    nama_pelanggan varchar(100) not null,
    no_telp varchar(15),
    alamat text
);

create table motor (
    id_motor int primary key auto_increment,
    plat_nomor varchar(15) unique not null,
    merk varchar(50),
    tipe varchar(50),
    id_pelanggan int,
    foreign key (id_pelanggan) references pelanggan(id_pelanggan)
);

create table servis (
    id_servis int primary key auto_increment,
    id_motor int,
    tanggal_servis date,
    keluhan text,
    biaya decimal(10,2),
    foreign key (id_motor) references motor(id_motor)
);

alter table servis add column status_pembayaran varchar(20) default 'belum lunas';
💡 apa yang terjadi di sini?

kita membuat 3 tabel yang saling terhubung.

primary key & auto_increment membuat id unik otomatis (1, 2, 3...).

foreign key mengunci relasi agar data motor selalu punya pemilik, dan data servis selalu punya motor.

alter digunakan untuk memodifikasi struktur tabel yang sudah jadi.

📥 2. tahap dml: manajemen isi data
tahap ini adalah mengisi tabel dengan data nyata dan melakukan pembaruan jika ada perubahan informasi.

SQL
insert into pelanggan (nama_pelanggan, no_telp, alamat) values
('andi setiawan', '081122', 'jakarta'),
('geraldy', '082233', 'bandung'),
('novi', '083344', 'surabaya'),
('dewi puspa', '084455', 'malang'),
('opang', '085566', 'semarang'),
('fajar sidik', '086677', 'medan');

insert into motor (plat_nomor, merk, tipe, id_pelanggan) values
('b 1234 aaa', 'honda', 'vario', 1),
('d 5678 bbb', 'yamaha', 'nmax', 2),
('l 9012 ccc', 'suzuki', 'gsx', 3),
('n 3456 ddd', 'kawasaki', 'ninja', 4),
('h 7890 eee', 'honda', 'beat', 5),
('bk 111 fff', 'yamaha', 'mio', null);

insert into servis (id_motor, tanggal_servis, keluhan, biaya) values
(1, '2026-02-10', 'ganti oli', 150000),
(2, '2026-02-11', 'servis rem', 75000),
(3, '2026-02-12', 'ganti ban', 450000),
(4, '2026-02-13', 'turun mesin', 1200000),
(5, '2026-02-14', 'servis rutin', 250000);

update servis set status_pembayaran = 'lunas' where id_servis = 1;
update pelanggan set alamat = 'bandung kota' where nama_pelanggan = 'geraldy';
💡 apa yang terjadi di sini?

insert into digunakan untuk memasukkan minimal 5 data ke setiap tabel agar simulasi terlihat nyata.

update digunakan untuk merubah data spesifik (seperti mengganti alamat atau status lunas).

🔍 3. tahap kueri: analisis & join
tahap ini digunakan untuk menarik informasi penting dan menghubungkan data yang terpisah.

SQL
-- gabungkan data pelanggan, motor, dan servis
select 
    pelanggan.nama_pelanggan, 
    motor.plat_nomor, 
    servis.tanggal_servis, 
    servis.keluhan, 
    servis.biaya
from pelanggan
join motor on pelanggan.id_pelanggan = motor.id_pelanggan
join servis on motor.id_motor = servis.id_motor;

-- tampilkan semua pelanggan meskipun belum ada motor (left join)
select pelanggan.nama_pelanggan, motor.plat_nomor 
from pelanggan 
left join motor on pelanggan.id_pelanggan = motor.id_pelanggan;

-- tampilkan semua motor meskipun belum ada pemilik (right join)
select pelanggan.nama_pelanggan, motor.plat_nomor 
from pelanggan 
right join motor on pelanggan.id_pelanggan = motor.id_pelanggan;

-- simulasi full join (mengambil semua data dari kedua tabel)
select pelanggan.nama_pelanggan, motor.plat_nomor from pelanggan left join motor on pelanggan.id_pelanggan = motor.id_pelanggan
union
select pelanggan.nama_pelanggan, motor.plat_nomor from pelanggan right join motor on pelanggan.id_pelanggan = motor.id_pelanggan;

-- hitung total transaksi servis (count)
select count(*) as total_transaksi from servis;

-- cari motor honda atau suzuki (or)
select * from motor where merk = 'honda' or merk = 'suzuki';

-- tampilkan 3 servis termahal (limit)
select * from servis order by biaya desc limit 3;
💡 apa yang terjadi di sini?

join adalah "lem" yang menyatukan tabel-tabel terpisah menjadi satu laporan.

count, or, dan limit adalah alat bantu untuk menyaring dan menghitung data dengan cepat.

📑 4. tahap view: laporan otomatis
tahap terakhir adalah membungkus kueri yang panjang ke dalam sebuah tabel virtual (view) agar mudah dipanggil sewaktu-waktu.

SQL
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

-- cara memanggil laporan secara instan
select * from laporan_harian_bengkel;
💡 apa yang terjadi di sini?

view menyimpan kueri join yang panjang menjadi satu nama pendek. admin bengkel tidak perlu mengetik kueri rumit setiap pagi, cukup panggil nama view-nya saja. ✨
