---
layout: post
title: "Belajar CRUD Sederhana PHP - MySQL"
date: 2020-02-02 19:18:00 +0700
categories: [php, mysql]
tags: [php, mysql, crud]
thumbnail: "/assets/img/thumbnail/php-mysql.jpg"
timeread: 20 min
excerpt_separator: <!--more-->
comments: true
comment_button: true
related: true
---
Sesuai dengan judul yang ada di atas, hari ini saya akan share cara membuat program CRUD sederhana dengan menggunakan bahasa <!--more-->program PHP dan database MySQL / Maria DB.

CRUD merupakan akronim / singkatan dari **C**reate **R**ead **U**pdate dan **D**elete, dengan kata lain kita akan membuat sebuah program yang menangani proses _Tambah data_, _Baca data_, _Edit data_, dan _Hapus data_.

> Disini saya asumsikan anda sudah mempersiapkan lokal server, text editor, dan browser untuk mengikuti tutorial [Belajar CRUD Sederhana PHP - MySQL][self-link]

Baiklah tanpa berpanjang lebar lagi, mari kita langsung ke poin tutorial Belajar CRUD Sederhana PHP - MySQL.


### Persiapan

* Nyalakan server lokal anda
* Buat Database baru
  * Buat sebuah Tabel baru
* Download Bootstrap [disini](https://getbootstrap.com/3.3/)
* Buka text editor anda
  * Ada 6 (enam) file yang akan kita buat pada tutorial ini :
    1. `koneksi.php`, file koneksi php ke database
    2. `create.php`, berfungsi untuk tambah data
    3. `read.php`, berfungsi untuk lihat / baca data
    4. `update.php`, berfungsi untuk edit data
    5. `delete.php`, berfungsi untuk hapus data
    6. `index.php`, berfungsi untuk navigasi halaman
* Secangkir Kopi dan cemilan.

#### Pembuatan Database dan Tabel

Silahkan buka browser anda kemudian buka [http://localhost/phpmyadmin](http://localhost/phpmyadmin){:target='blank'} (XAMPP / WAMPP) sudah menyediakan kemudahan untuk anda, untuk mengikuti tutorial ini silahkan buat sebuah database dengan nama **db_latihan**.

Setelah database berhasil dibuat, silahkan pilih menu SQL kemudian ketik perintah berikut untuk membuat sebuah tabel baru.

```sql
CREATE TABLE `tb_kontak` (
  `id` INT(11) NOT NULL PRIMARY KEY AUTO_INCREMENT,
  `nama` VARCHAR(50),
  `telp` VARCHAR(15),
  `email` VARCHAR(30)
);
```

#### File Koneksi PHP ke MySQL

Sesuai dengan namanya, file ini berfungsi untuk menghubungkan PHP dengan Database MySQL. Silahkan buka text editor anda, kemudian ketik kode berikut lalu simpan dengan nama `koneksi.php`:

```php
<?php
$DBHOST = "localhost"; // nama host atau server
$DBUSER = "root"; // nama pengguna database
$DBPASS = ""; // password database (XAMPP biasanya kosong)
$DBNAME = "db_latihan"; // nama database yang akan digunakan

// koneksi yang akan digunakan
$conn = new mysqli($DBHOST, $DBUSER, $DBPASS, $DBNAME);

// Testing koneksi, jika ada konfigurasi yang salah -
// maka akan muncul pesan error
if ($conn->connect_errno) {
  exit("Koneksi error {$conn->connect_errno} : {$conn->connect_error}");
}

// Bootstrap Template - Header Part
function template_header() {
  return <<<HTML
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=Edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Belajar CRUD Sederhana PHP-MySQL</title>
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
</head>
<body>
  <div class="container">
    <div class="row">
      <div class="col-md-8 col-md-offset-2">
        <h1 class="page-header">
          Belajar CRUD <kbd>PHP</kbd><code>MySQL</code>
        </h1>
HTML;
}

// Bootstrap Template - Footer Part
function template_footer() {
    return <<<HTML
      </div>
    </div>
  </div>
</body>
</html>
HTML;
}
?>
```

{:.alert.alert-info}
_**Info** Penjelasan Kode `koneksi.php`_

1. Pada baris (2-5) silahkan sesuaikan dengan pengaturan server anda, jika anda menggunakan lokal server (XAMPP / WAMPP) pengaturan tersebut sudah bisa langsung digunakan.
  * `$DBHOST` nama hosting anda _"umumnya localhost, meskipun di hosting tetep aja localhost.."_
  * `$DBUSER` nama pengguna / username mysql anda.
  * `$DBPASS` password mysql anda.
  * `$DBNAME` nama database yang akan digunakan, dalam tutorial ini **db_latihan**.
2. Pada baris ke-8, kita melakukakn inisialisasi koneksi PHP ke MySQL dengan bantuan [MySQLi Driver](https://www.php.net/manual/en/class.mysqli.php){:target='blank'}.
  * `$conn = new mysqli(host, user, pass, database);`
3. Pada baris (12-14) kita melakukan pengecekan, jika ada kesalahan / typo. Maka pesan error akan muncul.
4. Pada baris (17-47) ini adalah template dengan menggunakan [Bootstrap](https://getbootstrap.com/3.3/){:target='blank'} untuk mempermudah pembuatan struktur HTML dan mempercantik tampilan.
  * `template_header()` untuk membuat struktur bagian atas
  * `template_footer()` untuk membuat struktur bagian bawah / tag penutup

---

#### Tambah Data / Create

Setelah database, tabel, dan file koneksi berhasil dibuat. Tiba saatnya untuk melakukan eksperimen **Create** atau tambah data. Lanjut buka text editor anda, kemudian buat sebuah file dengan nama `create.php`. Lalu ketik kode berikut :

```php
<?php
// panggil file koneksi
require 'koneksi.php';

// cek apakah metode request = POST
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
  $nama = htmlspecialchars($_POST['nama']);
  $telp = htmlspecialchars($_POST['telp']);
  $email = htmlspecialchars(htmlentities($_POST['email']));
  $query = "INSERT INTO tb_kontak SET nama='$nama', telp='$telp', email='$email'";
  if ($conn->query($query)) {
    $conn->close();
    header("location: index.php");
  }
}

// panggil fungsi template header yang sudah dibuat sebelumnya
echo template_header();
?>

<!-- Link kembali ke halaman index.php -->
<p>
  <a href="index.php" class="btn btn-default">
    <span class="glyphicon glyphicon-chevron-left"></span> Kembali
  </a>
</p>

<!-- Form Tambah Data -->
<div class="panel panel-default">
  <div class="panel-heading">
    <h4 class="panel-title">Form Tambah Data</h4>
  </div>
  <form action="" method="POST">
    <div class="panel-body">
      <div class="form-group">
        <label>Nama</label>
        <input type="text" name="nama" class="form-control" placeholder="Nama Lengkap" required="">
      </div>
      <div class="form-group">
        <label>No.Telepon</label>
        <input type="number" name="telp" class="form-control" placeholder="08xx" required="">
      </div>
      <div class="form-group">
        <label>Alamat Email</label>
        <input type="email" name="email" class="form-control" placeholder="email@domain.com" required="">
      </div>
    </div>
    <div class="panel-footer">
      <button type="submit" class="btn btn-success">
        <span class="glyphicon glyphicon-ok"></span> Simpan
      </button> &nbsp; 
      <a href="index.php" class="btn btn-warning">
        <span class="glyphicon glyphicon-remove"></span> Batal
      </a>
    </div>
  </form>
</div>

<?php
// panggil fungsi template footer yang sudah dibuat sebelumnya
echo template_footer();
?>
```

{:.alert.alert-info}
_**Info** Penjelasan Kode `create.php`_

1. Pada baris ke-3, kita memanggil file `koneksi.php` yang sudah dibuat sebelumnya.
2. Baris (6-15), kita melakukan pengecekan apakah metode request yang dikirim adalah POST ? Jika benar, maka proses data yang dikirimkan oleh Form.
  * Ada 2 metode yang umum digunakan, yaitu **GET** dan **POST**. Untuk pengiriman data yang berasal dari form, sangat disarankan menggunakan metode **POST**. Sementara metode **GET** biasanya digunakan untuk fungsi Delete dan pre-Update.
  * Baris ke-12, `$conn->close()` tutup koneksi jika sudah selesai digunakan.
3. Baris ke-18, kita panggil fungsi `template_header()` untuk mempermudah dalam pembuatan struktur HTML dan mempercantik tampilan dengan Bootstrap Framework.
4. Baris (29-57), adalah Form yang bertugas menampung data yang akan ditambahkan. Untuk kemudian dikirim dengan metode POST seperti yang sudah dijelaskan pada **point 2**.
5. Baris ke-61, panggil fungsi `template_footer()` untuk menutup tag HTML yang sudah kita buka pada **point 3** fungsi `template_header()`.

---

#### Baca Data / Read

Setelah berhasil menambahkan data, maka rasa penasaran pun muncul..

> Yang barusan di posting dari Form, data nya beneran masuk gak ke database ??

Jika anda mengetik ulang koding yang saya share dengan benar, dan membaca inline comment yang ada pada kodingan. Seharusnya bisa langsung cek di _phpmyadmin_ untuk memastikan nya.

Mari kita lanjutkan tutorial **Belajar CRUD Sederhana PHP - MySQL**, buat sebuah file dengan nama `index.php` kemudian ketik kode berikut :

```php
<?php
require 'koneksi.php';

// query untuk mengambil semua data yang ada pada tabel "tb_kontak"
$query = "SELECT * FROM tb_kontak";
$data = $conn->query($query);

// panggil fungsi template header yang sudah dibuat sebelumnya
echo template_header();
?>

<!-- Link kembali ke halaman index.php -->
<p class="text-right">
  <a href="create.php" class="btn btn-primary">Tambah Data</a>
</p>

<!-- Tabel untuk Menmpilkan Data -->
<table class="table table-bordered table-striped table-hover">
  <thead>
    <tr>
      <th>#</th>
      <th>Nama</th>
      <th>Telepon</th>
      <th>Email</th>
      <th>Aksi</th>
    </tr>
  </thead>
  <tbody>
    <?php
    // cek apakah query yang dijalankan diatas berisi data / kosong
    if ($data->num_rows > 0) {
      // jika berisi data, lakukan perulangan / looping dengan metode WHILE
      while ($rs = $data->fetch_assoc()):?>
        <tr>
          <td width="5%"><?php echo $rs['id']?></td>
          <td><?php echo $rs['nama']?></td>
          <td><?php echo $rs['telp']?></td>
          <td><?php echo $rs['email']?></td>
          <td width="20%">
            <a href="update.php?id=<?php echo $rs['id']?>" class="btn btn-warning btn-xs">Update</a> &nbsp; 
            <a href="delete.php?id=<?php echo $rs['id']?>" class="btn btn-danger btn-xs" onclick="return confirm('Apakah anda yakin ingin menghapus data?')">Delete</a>
          </td>
        </tr>
      <?php
      endwhile;
    } else {
      // jika data kosong, maka tampilkan pesan berikut
      echo '<tr><td colspan="5" align="center">Data masih kosong</td></tr>';
    }?>
  </tbody>
</table>

<?php
// panggil fungsi template footer yang sudah dibuat sebelumnya
echo template_footer();
$conn->close();
?>
```

{:.alert.alert-info}
_**Info** Penjelasan Kode `index.php`_

File `index.php` adalah file yang akan di-Load pertama kali oleh server, itulah alasan saya kenapa tidak membuat file **read.php** meskipun judul nya CRUD.

Tapi yaa sudahlah, nama nya juga latihan. Yang penting bisa dulu baru modifikasi sesuai kebutuhan, :)

1. Pada baris ke-2, kita memanggil file `koneksi.php` yang sudah dibuat sebelumnya.
2. Baris (5-6), kita membuat perintah pemanggilan data / query.
  * `$conn` adalah koneksi yang sudah dibuat sebelumnya
  * `$conn->query( isi_perintah )` adalah salah satu diantara **banyaknya** cara memanggil data MySQL dari kode PHP dengan konsep Procedural
3. Baris ke-9, kita panggil fungsi `template_header()` untuk mempermudah dalam pembuatan struktur HTML dan mempercantik tampilan dengan Bootstrap Framework.
4. Baris (18-51), adalah Tabel yang bertugas menampilkan data dari hasil query pada **point 2**.
  * Baris ke-31, kita melakukan pengecekan terhadap query yang kita panggil. Apakah hasil query berisi data / kosong.
  * Baris (33-45), Jika kondisi berisi data maka kita lakukan Looping / perulangan data dengan metode **while**. Kemudian tampilkan kedalam tabel dengan perintah `echo $rs[nama_field]`. _`$rs` disini adalah variabel / alias, anda bebas menggunakan nama apapun yang diinginkan_.
  * Baris ke-40, disini kita membuat link untuk melakukan Update data dengan menggunakan metode **GET** ke file `update.php` dengan mengirimkan **id** sebagai parameter request nya. Yang akan kita bahas setelah ini.
  * Baris ke-41, disini kita membuat link untuk melakukan Hapus data dengan menggunakan metode **GET** ke file `delete.php` dengan mengirimkan **id** sebagai parameter request nya. Yang akan kita bahas setelah ini. Sedikit perbedaan dengan link update, pada link delete / hapus ini, saya menambahkan sedikit kode `JavaScript` untuk menampilkan konfirmasi sebelum data dihapus _(mencegah lebih baik daripada mengobati).. wkwkwk.._
  * Baris ke-48, Jika kondisi data kosong. Maka tampilkan pesan data kosong.
5. Baris ke-55, panggil fungsi `template_footer()` untuk menutup tag HTML yang sudah kita buka pada **point 3** fungsi `template_header()`.
6. Baris ke-56, `$conn->close()` tutup koneksi jika sudah selesai digunakan.

---

#### Ubah data / Update

Dalam operasi Update / Ubah data, sebenarnya hampir sama dengan cara Tambah data. Yang membedakan nya, pada operasi Update kita melakukan penarikan data yang sudah ada di database berdasarkan `id` untuk kemudian di tampilkan pada Form Update data.

Silahkan buat sebuah file, kemudian simpan dengan nama `update.php`. Lalu ketik kode berikut :

```php
<?php
require 'koneksi.php';

// lakukan pengecekan parameter `id` yang dikirim
if (isset($_GET['id'])) {
  $id = $_GET['id'];
  $query = "SELECT * FROM tb_kontak WHERE id=$id";
  $rs = $conn->query($query)->fetch_assoc();
}

// cek apakah metode request = POST
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
  $nama = htmlspecialchars($_POST['nama']);
  $telp = htmlspecialchars($_POST['telp']);
  $email = htmlspecialchars(htmlentities($_POST['email']));
  $id = $_POST['id'];
  $query = "UPDATE tb_kontak SET nama='$nama', telp='$telp', email='$email' WHERE id=$id";
  if ($conn->query($query)) {
    $conn->close();
    header("location: index.php");
  }
}

// panggil fungsi template header yang sudah dibuat sebelumnya
echo template_header();
?>

<!-- Link kembali ke halaman index.php -->
<p>
  <a href="index.php" class="btn btn-default">
    <span class="glyphicon glyphicon-chevron-left"></span> Kembali
  </a>
</p>

<!-- Form Update Data -->
<div class="panel panel-default">
  <div class="panel-heading">
    <h4 class="panel-title">Form Update Data</h4>
  </div>
  <form action="" method="POST">
    <div class="panel-body">
      <div class="form-group">
        <label for="">Nama</label>
        <input type="text" name="nama" value="<?php echo $rs['nama']?>" class="form-control" placeholder="Nama Lengkap" required="">
      </div>
      <div class="form-group">
        <label for="">No.Telepon</label>
        <input type="number" name="telp" value="<?php echo $rs['telp']?>" class="form-control" placeholder="08xx" required="">
      </div>
      <div class="form-group">
        <label for="">Alamat Email</label>
        <input type="email" name="email" value="<?php echo $rs['email']?>" class="form-control" placeholder="email@domain.com" required="">
      </div>
    </div>
    <div class="panel-footer">
      <button type="submit" class="btn btn-success">
        <span class="glyphicon glyphicon-ok"></span> Simpan
      </button> &nbsp; 
      <a href="index.php" class="btn btn-warning">
        <span class="glyphicon glyphicon-remove"></span> Batal
      </a>
    </div>
    <input type="hidden" name="id" value="<?php echo $rs['id']?>">
  </form>
</div>

<?php
// panggil fungsi template footer yang sudah dibuat sebelumnya
echo template_footer();
$conn->close();
?>
```

{:.alert.alert-info}
_**Info** Penjelasan Kode `update.php`_

1. Pada baris (5-9), kita melakukan pengecekan parameter `id` yang dikirim. Kemudian melakukan tarik data ke database berdasarkan `id` yang diminta.
2. Baris (12-22), kode ini berfungsi untuk menangkap hasil submit form update. Kemudian melakukan update data ke database berdasarkan data yang dikirim dari form.
3. Perhatikan baris (44,48,52, dan 63), disana terdapat kode `<?php echo $rs['...']?>`. Cuplikan kode tersebut berfungsi untuk menyisipkan value / data, dan menampilkan nya kedalam kolom inputan. Seperti yang sudah saya jelaskan pada **point 1** berdasarkan `id` yang dikirim oleh user.
4. Baris ke-70, seperti biasa kita tutup koneksi setelah selesai digunakan.

---

#### Hapus data / Delete

Tambah data _sudah_, Baca data _sudah_, Edit data _sudah_, berarti yang terakhir dari tutorial **Belajar CRUD Sederhana PHP - MySQL** kali ini tinggal Hapus data.

Koding Hapus data terbilang cukup simpel, bahkan sangat simpel. Penjelasan kode pada inline comment, Silahkan buat sebuah file dengan nama `delete.php` kemudian ketik kode berikut :

```php
<?php
require 'koneksi.php';

// lakukan pengecekan parameter yang dikirim
if (isset($_GET['id'])) {
  // jika parameter memenuhi kriteria, eksekusi perintah hapus data;

  // tampung id kedalam variabel
  $id = $_GET['id'];
  
  // query hapus data berdasarkan `id` yang dikirimkan
  $query = "DELETE FROM tb_kontak WHERE id=$id";
  
  // eksekusi query delete / hapus data
  if ($conn->query($query)) {
    // jika query berhasil dijalankan

    // tutup koneksi
    $conn->close();
    
    // kembali ke halaman "index.php"
    header("location: index.php");
  }
}
?>
```

Bagaimana? Cukup mudah dipahami bukan tutorial nya.

---

### Summary

Semua program-program hebat, mulai dari marketplace, e-commerce, hingga platform besar yang ada saat ini, berawal dari CRUD. Yang membedakan antara platform satu dengan yang lain nya adalah tingkat kreatifitas sang programmer dan kompleksitas proses bisnis yang mereka jalankan dalam kebutuhan mengolah dan menyajikan data.

Sekian tutorial [Belajar CRUD Sederhana PHP - MySQL][self-link] kali ini, semoga bermanfaat bagi anda. Jika ada yang kurang jelas silahkan tanyakan di kolom komentar, sampai jumpa di tutorial lain nya.

> Code is art's

[self-link]: https://cikal/github.io/php/mysql/2020/02/02/php-mysqli-crud.html