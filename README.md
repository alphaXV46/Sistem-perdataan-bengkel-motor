# Sistem-perdataan-bengkel-motor
bengkel motor kelompok 7
🛠️ dokumentasi lengkap: database bengkel motor
project ini merupakan simulasi sistem manajemen database untuk bengkel motor yang mengintegrasikan tiga tabel utama dengan relasi yang saling terhubung.

🏗️ 1. struktur database (ddl)
tahap awal adalah membangun pondasi database. di sini kita menggunakan perintah create dan alter untuk mengatur struktur.

SQL
-- pembuatan tabel pelanggan, motor, dan servis
-- menambahkan kolom 'status_pembayaran' menggunakan perintah alter
tabel pelanggan: menyimpan data utama pemilik.

tabel motor: menyimpan detail kendaraan dengan referensi ke pemilik.

tabel servis: menyimpan catatan transaksi perbaikan.

📥 2. manajemen data (dml)
berikut adalah cara memasukkan data (insert) dan memperbarui data (update) yang sudah ada. dalam project ini, setiap tabel diisi minimal 5 data untuk keperluan simulasi.

insert: menambahkan data baru ke tiap tabel (pastikan kata kunci values digunakan).

update: mengubah data, misalnya mengubah status pembayaran dari 'belum lunas' menjadi 'lunas'.

📊 3. teknik pengambilan data (query & join)
bagian terpenting untuk menghasilkan informasi dari data yang tersebar di berbagai tabel.

a. jenis-jenis join
inner join: menampilkan hanya pelanggan yang sudah memiliki riwayat servis.

left join: menampilkan semua pelanggan, termasuk yang belum mendaftarkan motornya.

right join: menampilkan semua data motor, termasuk yang data pemiliknya belum tercatat.

full join (union): menampilkan seluruh data dari semua tabel tanpa terkecuali.

b. filter & agregasi
count: digunakan untuk menghitung total motor atau jumlah transaksi servis.

or: digunakan untuk mencari data dengan beberapa kondisi (contoh: merk honda atau yamaha).

limit: digunakan untuk membatasi tampilan data (contoh: menampilkan 5 transaksi terakhir).

🖼️ 4. laporan otomatis (view)
untuk mempermudah admin bengkel, kita membuat view sebagai jalan pintas laporan. kita tidak perlu menulis kueri join yang panjang setiap saat, cukup panggil nama view-nya saja.

SQL
-- contoh memanggil laporan lewat view
select * from laporan_servis_lengkap;
📋 contoh kueri laporan (tanpa alias)
berikut adalah kueri gabungan yang paling sering digunakan untuk melihat data secara utuh:

SQL
select 
    pelanggan.nama_pelanggan, 
    motor.plat_nomor, 
    motor.merk, 
    servis.tanggal_servis, 
    servis.keluhan, 
    servis.biaya
from pelanggan
join motor on pelanggan.id_pelanggan = motor.id_pelanggan
join servis on motor.id_motor = servis.id_motor;
💡 kesimpulan materi
melalui project ini, kita telah mempraktikkan:

ddl: membuat dan mengubah struktur.

dml: mengisi dan memperbaiki data.

join: menghubungkan tabel-tabel terpisah.

view: meringkas kueri rumit menjadi sederhana.

fungsi dasar: menghitung (count), menyaring (or), dan membatasi (limit).
