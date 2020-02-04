---
layout: post
title: "Membuat Form Pencarian dengan AJAX & PHP"
date: 2020-02-05 00:31:15 +0700
categories: [php, mysql, javascript]
tags: [php, mysql, javascript, ajax]
images: "/assets/img/php-mysql-ajax-pencarian-1.png"
thumbnail: "/assets/img/thumbnail/php-mysql-ajax-pencarian-1.png"
timeread: 20 min
excerpt_separator: <!--more-->
comments: true
comment_button: false
related: true
---
Halo jumpa kembali di blog Cikal, pada kesempatan kali ini saya akan membagikan tutorial [Membuat Form Pencarian dengan AJAX & PHP][self-link].<!--more-->

Studi kasus yang akan kita gunakan kali ini adalah **Form Pencarian Barang**. Sangat umum dan familiar, karena sering kita jumpai di situs-situs Marketplace yang ada.

Pada tutorial ini, kita akan menggunakan 5 (lima) bahasa pemrograman (**Wadaw...**). _`HTML`, `CSS`, `Javascript`, `PHP`,_ dan _`MySQL`_.

Yang lebih menarik lagi kita akan bermain dengan _Javascript Native_, karena ketergantungan kita dengan kemudahan yang diberikan oleh **jQuery** sampai-sampai _hampir kebanyakan_ dari programmer malah tidak mengetahui cara menggunakan AJAX dengan Javascript Native tanpa jQuery.

Padahal Javascript Native (jaman Now), sudah berkembang dengan sangat cepat. Bisa dibilang lebih asik dari sebelumnya, beberapa diantaranya yang sering saya gunakan _`Promise`, `Async`, `Fetch`, `Arrow Function`, dll_.

Mari kita lanjut ke tutorial **Membuat Form Pencarian dengan AJAX & PHP**.

---

> _**Apa itu AJAX ?**_

---

Dilansir dari situs WikiPedia bahasa Indo [https://id.wikipedia.org/wiki/AJAX](https://id.wikipedia.org/wiki/AJAX).

{:.alert.alert-primary}
**A**synchronous **J**avaScript **a**nd **X**MLHTTP, atau disingkat **AJaX**, adalah suatu teknik pemrograman berbasis web untuk menciptakan aplikasi web interaktif. Tujuannya adalah untuk memindahkan sebagian besar interaksi pada komputer web surfer, melakukan pertukaran data dengan server di belakang layar, sehingga halaman web tidak harus dibaca ulang secara keseluruhan setiap kali seorang pengguna melakukan perubahan. Hal ini akan meningkatkan interaktivitas, kecepatan, dan usability.

Ajax merupakan kombinasi dari:

* DOM yang diakses dengan client side scripting language, seperti VBScript dan implementasi ECMAScript seperti JavaScript dan JScript, untuk menampilkan secara dinamis dan berinteraksi dengan informasi yang ditampilkan
* Objek XMLHTTP dari Microsoft atau XMLHttpRequest yang lebih umum di implementasikan pada beberapa browser. Objek ini berguna sebagai kendaraan pertukaran data asinkronus dengan web server. Pada beberapa framework AJAX, element HTML IFrame lebih dipilih daripada XMLHTTP atau XMLHttpRequest untuk melakukan pertukaran data dengan web server.
* XML umumnya digunakan sebagai dokumen transfer, walaupun format lain juga memungkinkan, seperti HTML, plain text. XML dianjurkan dalam pemakaian teknik AJaX karena kemudahan akses penanganannya dengan memakai DOM
* JSON dapat menjadi pilihan alternatif sebagai dokumen transfer, mengingat JSON adalah JavaScript itu sendiri sehingga penanganannya lebih mudah

---

Sederhananya AJAX adalah satu tehnik pemrograman yang membuat aplikasi web kita lebih interaktif, karena pertukaran data dlakukan secara _asynchronous_ atau di balik layar. Jadi pengunjung / client web kita disuguhkan pengalaman yang asik karena adanya fitur AJaX ini (User Experience / UX).

Saya rasa cukup untuk pembukaan dan intermezzo nya, biar makin joss kita langsung bermain ke koding.

---

### Persiapan

* Nyalakan server lokal anda
* Buat Database baru dan 2 (dua) Tabel baru
* Buka text editor anda, Ada 5 (lima) file yang akan kita buat pada tutorial ini :
  1. `koneksi.php`, file koneksi php ke database
  2. `search.php`, berfungsi untuk merespon hasil pencarian
  3. `index.html`, berfungsi untuk menampilkan form pencarian
  4. `style.css`, berfungsi untuk mempercantik tampilan
  5. `script.js`, file utama pada tutorial AJAX ini

---

#### Pembuatan Database dan Tabel

Silahkan buka browser anda kemudian buka [http://localhost/phpmyadmin](http://localhost/phpmyadmin){:target='blank'} (XAMPP / WAMPP) sudah menyediakan kemudahan untuk anda, untuk mengikuti tutorial ini silahkan buat sebuah database dengan nama **db_latihan**.

Setelah database berhasil dibuat, silahkan pilih menu SQL kemudian ketik perintah berikut untuk membuat 2 (dua) tabel baru.

```sql
-- TABEL KATEGORI
CREATE TABLE `tb_kategori` (
  `id` INT(11) NOT NULL PRIMARY KEY AUTO_INCREMENT,
  `nama_kategori` VARCHAR(50)
);

-- TABEL BARANG
CREATE TABLE `tb_barang` (
  `id` INT(11) NOT NULL PRIMARY KEY AUTO_INCREMENT,
  `kode_barang` VARCHAR(10),
  `nama_barang` VARCHAR(50),
  `harga` INT(20),
  `kat_id` INT(11) NOT NULL
);
```

{:.alert.alert-info}
_**Info** Penjelasan Kode_

* **tb_kategori**, digunakan untuk menampung kategori barang.
* **tb_barang**, digunakan untuk menampung barang dengan relasi ke tabel kategori berdasarkan `kat_id` atau `id` kategori.

Silahkan tambahkan beberapa data kedalam tabel-tabel tersebut, atau anda bisa juga menggunakan program pada tutorial sebelumnya [Belajar CRUD Sederhana PHP - MySQL][old-post].

```sql
-- DUMMY DATA KATEGORI
INSERT INTO `tb_kategori` (`nama_kategori`,`keterangan`) VALUES 
('KOPI'),
('ROKOK'),
('SNACK & BISCUIT');

-- DUMMY DATA BARANG
INSERT INTO `tb_barang` (`kode_barang`,`nama_barang`,`harga`,`kat_id`) VALUES 
('BR-100-001','KAPAL API SPC MIX 10s',11500,1),
('BR-100-002','GOOD DAY CPCN 10s',16500,1),
('BR-100-003','INDOCAFE COFFEEMIX 10s',12500,1),
('BR-200-001','MARLBORO LIGHT 20',28000,2),
('BR-200-002','GG FILTER 12',18500,2),
('BR-200-003','SAMPOERNA MLD 16',24000,2),
('BR-300-001','ROMA SR-GANDUM CKLT 115g',7500,3),
('BR-300-002','ROMA MALKIST CKLT 153g',6800,3),
('BR-300-003','CHITATO BBQ 250g',39500,3);
```

---

#### File Koneksi PHP ke MySQL

Sesuai dengan namanya, file ini berfungsi untuk menghubungkan PHP dengan Database MySQL. Silahkan buka text editor anda, kemudian ketik kode berikut lalu simpan dengan nama `koneksi.php`:

```php
<?php
$DBHOST = "localhost";  // nama host atau server
$DBUSER = "root";       // nama pengguna database
$DBPASS = "";           // password database (XAMPP biasanya kosong)
$DBNAME = "db_latihan"; // nama database yang akan digunakan

// koneksi yang akan digunakan
$conn = new mysqli($DBHOST, $DBUSER, $DBPASS, $DBNAME);

// Testing koneksi, jika ada konfigurasi yang salah -
// maka akan muncul pesan error
if ($conn->connect_errno) {
  exit("Koneksi error {$conn->connect_errno} : {$conn->connect_error}");
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

---

#### File Pencarian / Search

File ini berfungsi untuk menangkap request dari Pengunjung / Client yang dikirim via AJAX, dengan kata lain file ini akan menerima request berupa keyword pencarian dan mengembalikan hasil dalam bentuk JSON.

Silahkan buka text editor anda, kemudian ketik kode berikut lalu simpan dengan nama `search.php`:

```php
<?php
// panggil file koneksi
require 'koneksi.php';

// set header respon sebagai json
header('Content-Type: application/json');

// pengecekan hanya method POST yang diperbolehkan
if ($_SERVER['REQUEST_METHOD'] == 'POST') {

  // jika request mengandung parameter `key`
  if (isset($_POST['key'])) {

    // tampung keyword kedalam variabel
    $keyword = htmlspecialchars($_POST['key']);

    // query pencarian dengan `JOIN` data dari tabel Barang
    // ke tabel Kategori berdasarkan `id` tabel Kategori
    $query ="SELECT
        b.nama_barang, b.harga, k.nama_kategori AS kategori
      FROM `tb_barang` AS b
      LEFT JOIN `tb_kategori` AS k 
        ON b.kat_id=k.id
      WHERE b.nama_barang
        LIKE '%".$keyword."%' OR k.nama_kategori LIKE '%".$keyword."%'";

    // eksekusi query
    $data = $conn->query($query);

    if ($data->num_rows > 0) {

      // jika query yang dijalankan berisi data maka
      $output = array(
        "status" => "sukses",
        "pesan" => "ada $data->num_rows ditemukan",
        "data" => $data->fetch_all(MYSQLI_ASSOC)
      );
    } else {

      // jika query yang dijalankan kosong maka
      $output = array(
        "status" => "sukses",
        "pesan" => "data tidak ditemukan",
        "data" => []
      );
    }

    // output enkode data dalam bentuk JSON
    echo json_encode($output);
  } else {

    // jika request TIDAK mengandung parameter `key`
    // maka tarik semua data Barang dengan JOIN tabel Kategori
    $query = "SELECT
      b.nama_barang, b.harga, k.nama_kategori AS kategori
    FROM `tb_barang` AS b
    LEFT JOIN `tb_kategori` AS k
      ON b.kat_id=k.id";

    // output enkode data dalam bentuk JSON
    echo json_encode($conn->query($query)->fetch_all(MYSQLI_ASSOC));
  }
  
} else {

  // jika bukan method POST maka pesan ini akan tampil
  // silahkan coba akses http://localhost/[nama_folder]/search.php
  die('No direct script access allowed');
}
?>
```

{:.alert.alert-info}
_**Info** Penjelasan Kode saya buat dalam inline comment_

---

#### File index untuk Menampilkan data dan Form pencarian

File inilah yang akan Pengunjung / Client kita gunakan untuk melakukan pencarian barang.

Silahkan buka text editor anda, kemudian ketik kode berikut lalu simpan dengan nama `index.html`:

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Membuat Form Pencarian dengan AJAX & PHP</title>
  <!-- panggil file css -->
  <link rel="stylesheet" href="style.css">
</head>

<body>
  <main class="container">

    <header>
      <h1>
        <span>Form Pencarian AJAX & PHP</span>
        <small><a href="https://cikal.github.io">cikal.github.io</a></small>
      </h1>
    </header>

    <br>
    
    <!-- form untuk melakukan pencarian -->
    <p>
      <input type="search" onkeyup="cari(this.value)" placeholder="Cari Nama Barang / Kategori">
    </p>

    <!-- tabel untuk me-render data -->
    <table width="100%" cellpadding="5" border="1" style="border-collapse: collapse;">
      <thead>
        <tr>
          <th>Nama Barang</th>
          <th>Harga</th>
          <th>Kategori</th>
        </tr>
      </thead>
      <tbody>
        <tr><td colspan="3" align="center">Masukkan pencarian</td></tr>
      </tbody>
    </table>

  </main>

  <footer>
    <a href="https://cikal.github.io">Copyright &copy; 2020 Blog Cikal</a>
  </footer>
  
  <!-- panggil file javascript -->
  <script src="script.js"></script>
</body>
</html>
```

---

#### File untuk Tampilan

File ini kita gunakan untuk mempercantik tampilan, jangan berharap ada _Bootstrap_ yaaa, karena pada tutorial ini saya ingin mengajak pengunjung untuk flashback ke Native Code. _:)_

Silahkan buka text editor anda, kemudian ketik kode berikut lalu simpan dengan nama `style.css`:

```css
body {
  background-color: #fafafa;
  font-family: Verdana, Arial, sans-serif;
  font-size: 14px;
  margin: auto;
  max-width: 700px;
  padding: 3em;
}

.container {
  background-color: #fff;
  border: 1px solid #aaa;
  border-radius: 4px;
  padding: 14px;
  box-shadow: 2px 2px 2px gray;
}

h1 {
  font-size: 120%;
  padding-bottom: 8px;
  margin-bottom: 1em;
  border-bottom: 2px solid #000;
}

h1 span {
  padding-bottom: 8px;
  margin-bottom: 1em;
  border-bottom: 2px solid tomato;
}

h1 small {
  float: right;
  color: #aaa;
  font-size: 70%
}

a {
  color: #aaa;
  text-decoration: none;
  transition: .5s
}

a:hover {
  color: #000;
  text-decoration: underline;
}

input {
  width: 100%;
  padding: 6px;
  border: 1px solid #ccc;
  border-radius: 4px
}

th,
td {
  padding: 5px;
}

tr:nth-child(even) {
  background-color: rgba(255, 99, 71, .2)
}

th {
  background-color: tomato;
  color: white;
  font-weight: normal;
}

footer {
  text-align: center;
  margin-top: 2em
}
```

---

#### File Engine / Javascript

File ini adalah topik utama dari tutorial kita kali ini [Membuat Form Pencarian dengan AJAX & PHP][self-link], mohon untuk membiasakan diri membaca comment inline dari kode yang saya share.

Kode AJAX yang saya buat dalam file ini, menggunakan 2 (dua) metode :

1. `XMLHttpRequest`, metode Jadul.
2. `Fetch API`, metode jaman now.

Mungkin beberapa dari anda masih setia menggunakan `XMLHttpRequest`, namun apa salahnya mencoba Fetch API yang lebih memberikan kemudahan dan sudah mendukung `Promise`. Baiklah tanpa basa-basi lagi langsung saja.

Silahkan buka text editor anda, kemudian ketik kode berikut lalu simpan dengan nama `script.js`:

```javascript
// panggil fungsi get_all() ketika halaman selesai di load sepenuhnya
document.addEventListener('DOMContentLoaded', get_all(), false);

// AJAX menggunakan Fetch API Javascript ES6
// dokumentasi (https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
function get_all() {
  fetch("search.php", { method: 'POST' })

    // deklarasikan data dalam bentuk JSON
    .then( (response) => response.json() )

    // manipulasi dan konsumsi data terjadi disini
    .then( (data) => {

      /**
       * console.table( array )
       * ---
       * hanya untuk testing, anda bisa langsung melihat data dalam -
       * bentuk tabel tanpa harus melakukan looping terlebih dahulu pada
       * konsol web yang anda gunakan, silahkan tekan F12 kemudian
       * klik pada tab `Console` untuk melihat nya.
       */
      console.table(data);

      // variabel penampung data hasil looping
      var dataset = "";

      // looping data dengan bantuan `forEach( callback )`
      data.forEach( (a, b) => {
        dataset += `<tr>
          <td>${a.nama_barang}</td>
          <td>${a.harga}</td>
          <td>${a.kategori}</td>
        </tr>`;
      });

      // masukkan data kedalam `<tbody> dataset </tbody>`
      document.querySelector('tbody').innerHTML = dataset;
    })

    // tangkap pesan error jika terjadi kesalahan pada saat proses
    .catch( (error) => {

      // tampilkan pesan error kedalam konsol
      console.error( error );
    });
}


// AJAX menggunakan XMLHttpRequest Javascript jadul.. ^_^
// dokumentasi (https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
function cari(key) {

  // fungsi pencarian akan dijalankan jika panjang keyword minimal 3 huruf
  if (key.length >= 3) {

    // variabel initial XMLHttpRequest
    var xhr = new XMLHttpRequest();

    /**
     * Jalankan fungsi ketika properti `readyState` dan `status` berubah
     * dokumentasi (https://www.w3schools.com/js/js_ajax_http_response.asp)
     * ---
     * Kode `readyState` :
     *   0: request not initialized
     *   1: server connection established
     *   2: request received
     *   3: processing request
     *   4: request finished and response is ready
     * ---
     * Kode `status` atau HTTP Code, yang umum digunakan :
     *   200: "OK"
     *   403: "Forbidden"
     *   404: "Page not found"
     */
    xhr.onreadystatechange = () => {
      if (xhr.readyState == 4 && xhr.status == 200) {

        /**
         * Jika request berhasil, response yang kita dapatkan
         * dalam bentuk JSON. Parsing JSON tersebut untuk kita
         * gunakan dalam looping pada tabel.
         */
        var hasil = JSON.parse(xhr.responseText);

        if (hasil.data.length) {

          // jika data nya berisi atau tidak kosong maka tampilkan
          // data kedalam tabel dengan bantuan looping `forEach`

          // dataset = variabel penampung data hasil looping
          var dataset = "";
          hasil.data.forEach((a,b) => {
            dataset += `<tr>
              <td>${a.nama_barang}</td>
              <td>${a.harga}</td>
              <td>${a.kategori}</td>
            </tr>`;
          })

          // masukkan data kedalam `<tbody> dataset </tbody>`
          document.querySelector('tbody').innerHTML = dataset;
        } else {
          // jika data kosong maka tampilkan pesan dari server
          document.querySelector('tbody').innerHTML = `<tr><td colspan="3" align="center">${hasil.pesan}</td></tr>`;
        }
      }
    }

    // eksekusi XMLHttpRequest dengan metode POST
    xhr.open("POST", "search.php", true);

    // set header nya sebagai request dari Form
    xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");

    // masukkan keyword pencarian ke dalam Form request
    xhr.send(`key=${key}`);
  } else {

    // jika panjang keyword tidak sama dengan 3 atau lebih ( ! key >= 3 )
    // maka tampilkan semua data dengan fungsi get_all()
    get_all();
  }
}
```

Bagaimana? Cukup mudah dipahami bukan tutorial nya.

![Console Table](/assets/img/php-mysql-ajax-pencarian-2.png){:.img-fluid}

---

### Summary

Penggunaan AJAX sangat membantu dalam banyak hal, terlepas dari fakta bahwa AJAX pula dapat menimbulkan permasalahan baru ketika kita ingin mengkonsumsi data.

---

### Tips-Trik

Ketika anda ingin menggunakan AJAX untuk penarikan data dari `https` ke `http` atau `Domain A` ke `Domain B`, anda mungkin akan menemukan kendala _**CORS**_ atau _**C**ross-**O**rigin **R**esource **S**haring_ adalah mekanisme yang memungkinkan sumber daya terbatas pada halaman web diminta dari domain lain di luar domain asal sumber.

Ilustrasi Sederhananya, saya memiliki 2 (dua) server.

1. **Server A** saya gunakan sebagai server data
2. **Server B** saya gunakan sebagai frontend

Ketika saya ingin memanggil data dari **Server B** ke **Server A** menggunakan AJAX, tiba-tiba data tidak muncul. Setelah cek konsol browser (F12) ada tulisan `merah` yang berisi surat cinta _wkwkwk.._

{:.alert.alert-warning}
`XMLHttpRequest cannot load` http://server-A.com/search.php `No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin '` http://server-B.com `'is therefore not allowed access.`

> Terus gimana dong ?

Trik nya cukup sederhana, namun anda harus memikirkan cara lain untuk mengamankan data anda. Silahkan buka file **php** yang digunakan, _Contoh seperti di tutorial ini file `search.php`_.

Tambahkan kode berikut pada line kedua setelah tag pembuka `<?php`:

```php
<?php
header('Access-Control-Allow-Origin: *');

# ------------------------------------
# isi kode program
# ------------------------------------

?>
```

Simpel bukan, tapi anda harus mulai mencari cara lain untuk mengamankan data yang anda miliki.

---

Sekian tutorial [Membuat Form Pencarian dengan AJAX & PHP][self-link] kali ini, semoga bermanfaat bagi anda. Jika ada yang kurang jelas, seperti biasa silahkan tanyakan di kolom komentar. Sampai jumpa di tutorial lain nya.

> _Biarkan **jari** mu senam di atas keyboard, sementara **otak** mu mencerna Logika._

Link referensi :

* [Wikipedia CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing){:target='blank'}
* [Wikipedia AJAX](https://id.wikipedia.org/wiki/AJAX){:target='blank'}
* [Mozilla Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API){:target='blank'}
* [Mozilla XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest){:target='blank'}

[self-link]: https://cikal.github.io/php/mysql/javascript/2020/02/05/php-ajax-pencarian.html
[old-post]: https://cikal.github.io/php/mysql/2020/02/02/php-mysqli-crud.html