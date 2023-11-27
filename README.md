# Lab8Web

# Praktikum 8: PHP dan Database MySQL

# INSTRUKSI PRAKTIKUM
1. Persiapkan text editor misalnya VSCode.
2. Buat folder baru dengan nama lab8_php_database pada docroot webserver
(htdocs)
3. Ikuti langkah-langkah praktikum yang akan dijelaskan berikutnya.

# Langkah-langkah Praktikum
## Persiapan
Untuk memulai membuat aplikasi CRUD sederhana, yang perlu disiapkan adalah
database server menggunakan MySQL. Pastikan MySQL Server sudah dapat dijalankan
melalui XAMPP.

## Mengakses MySQL Client menggunakan PHP MyAdmin
Pastikan webserver Apache dan MySQL server sudah dijalankan. Kemudian buka
melalui browser: http://localhost/phpmyadmin/

## Membuat Database: Studi Kasus Data Barang

![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/2bfbc759-5e86-45a1-928f-188e89440122)

## Membuat Database
`CREATE DATABASE latihan1;`

## Membuat Tabel
```
CREATE TABLE data_barang (
id_barang int(10) auto_increment Primary Key,
kategori varchar(30),
nama varchar(30),
gambar varchar(100),
harga_beli decimal(10,0),
harga_jual decimal(10,0),
stok int(4)
);
```
![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/dd56cce6-57b1-4a3e-862d-06f2088eb366)

## Menambahkan Data
```
INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
VALUES ('Elektronik', 'HP Samsung Android', 'hp_samsung.jpg', 2000000, 2400000, 5),
('Elektronik', 'HP Xiaomi Android', 'hp_xiaomi.jpg', 1000000, 1400000, 5),
('Elektronik', 'HP OPPO Android', 'hp_oppo.jpg', 1800000, 2300000, 5);
```
![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/16c480d1-feb6-446f-ae31-da53bc945188)

![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/f2769292-dbea-4c76-8317-4286dec40424)

## Membuat Program CRUD
Buat folder lab8_php_database pada root directory web server (d:\xampp\htdocs)

![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/97f78046-5cd6-4b0d-bb49-0ed5ca0a0632)

Kemudian untuk mengakses direktory tersebut pada web server dengan mengakses URL:
http://localhost/lab8_php_database/

![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/3c16910a-09c2-44cb-b02e-2cc6370123c8)

## Membuat file koneksi database
Buat file baru dengan nama koneksi.php
### Code
```
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db = "latihan1";
$conn = mysqli_connect($host, $user, $pass, $db);
if ($conn == false)
{
echo "Koneksi ke server gagal.";
die();
} #else echo "Koneksi berhasil";
?>
```

![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/8e1f6fd1-2fac-471f-b55d-0c45862c7031)

### Output

Buka melalui browser untuk menguji koneksi database (untuk menyampilkan pesan
koneksi berhasil, uncomment pada perintah echo “koneksi berhasil”;

![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/2383b517-c3d9-4f95-ae95-b425eb53f596)

## Membuat file index untuk menampilkan data (Read)
Buat file baru dengan nama index.php

```
<?php
include("koneksi.php");
// query untuk menampilkan data
$sql = 'SELECT * FROM data_barang';
$result = mysqli_query($conn, $sql);
?>
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<link href="style.css" rel="stylesheet" type="text/css" />
<title>Data Barang</title>
</head>
<body>
<div class="container">
<h1>Data Barang</h1>
<div class="main">
<table>
<tr>
<th>Gambar</th>
<th>Nama Barang</th>
<th>Katagori</th>
<th>Harga Jual</th>
<th>Harga Beli</th>
<th>Stok</th>
<th>Aksi</th>
</tr>
<?php if($result): ?>
<?php while($row = mysqli_fetch_array($result)): ?>
<tr>
<td><img src="gambar/<?= $row['gambar'];?>" alt="<?=
$row['nama'];?>"></td>
<td><?= $row['nama'];?></td>
<td><?= $row['kategori'];?></td>
<td><?= $row['harga_beli'];?></td>
<td><?= $row['harga_jual'];?></td>
<td><?= $row['stok'];?></td>
<td><?= $row['id_barang'];?></td>
</tr>
<?php endwhile; else: ?>
<tr>
<td colspan="7">Belum ada data</td>
</tr>
<?php endif; ?>
</table>
</div>
</div>
</body>
</html>
```

![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/522a3509-7b32-4198-870d-086292d2bce8)

## Menambah Data (Create)
Buat file baru dengan nama tambah.php

```
<?php
error_reporting(E_ALL);
include_once 'koneksi.php';

if (isset($_POST['submit']))
{
    $nama = $_POST['nama'];
    $kategori = $_POST['kategori'];
    $harga_jual = $_POST['harga_jual'];
    $harga_beli = $_POST['harga_beli'];
    $stok = $_POST['stok'];
    $file_gambar = $_FILES['file_gambar'];
    $gambar = null;
    if ($file_gambar['error'] == 0)
    {
        $filename = str_replace(' ', '_',$file_gambar['name']);
        $destination = dirname(__FILE__) .'/gambar/' . $filename;
        if(move_uploaded_file($file_gambar['tmp_name'], $destination))
        {
            $gambar = 'gambar/' . $filename;;
        }
    }
    $sql = 'INSERT INTO data_barang (nama, kategori,harga_jual, harga_beli, stok, gambar) ';
    $sql .= "VALUE ('{$nama}', '{$kategori}','{$harga_jual}', '{$harga_beli}', '{$stok}', '{$gambar}')";
    $result = mysqli_query($conn, $sql);
    header('location: index.php');
}

?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Tambah Barang</title>
</head>
<style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f7f7f7;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }

        .container {
            width: 400px;
            background: #fff;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            border-radius: 8px;
            overflow: hidden;
        }

        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 20px;
        }

        .main {
            padding: 20px;
        }

        .input {
            margin-bottom: 15px;
        }

        label {
            display: block;
            font-size: 14px;
            margin-bottom: 8px;
            color: #555;
        }

        input[type="text"],
        select,
        input[type="file"] {
            width: calc(100% - 16px);
            padding: 10px;
            box-sizing: border-box;
            margin-bottom: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-size: 14px;
        }

        .submit {
            text-align: center;
        }

        input[type="submit"] {
            background-color: #4caf50;
            color: white;
            padding: 12px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.3s;
        }

        input[type="submit"]:hover {
            background-color: #45a049;
        }
    </style>
<body>
<div class="container">
    <h1>Tambah Barang</h1>
    <div class="main">
        <form method="post" action="tambah.php"
        enctype="multipart/form-data">
        <div class="input">
            <label>Nama Barang</label>
            <input type="text" name="nama" />
        </div>
        <div class="input">
            <label>Kategori</label>
            <select name="kategori">
                <option value="Komputer">Komputer</option>
                <option value="Elektronik">Elektronik</option>
                <option value="Hand Phone">Hand Phone</option>
            </select>
        </div>
        <div class="input">
            <label>Harga Jual</label>
            <input type="text" name="harga_jual" />
        </div>
        <div class="input">
            <label>Harga Beli</label>
            <input type="text" name="harga_beli" />
        </div>
        <div class="input">
            <label>Stok</label>
            <input type="text" name="stok" />
        </div>
        <div class="input">
            <label>File Gambar</label>
            <input type="file" name="file_gambar" />
        </div>
        <div class="submit">
            <input type="submit" name="submit" value="Simpan" />
        </div>
    </form>
</div>
</div>
</body>
</html>
```
![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/099c380a-4ae3-454f-9a9e-b7727b00c76f)

## Mengubah Data (Update)
Buat file baru dengan nama ubah.php

```
<?php
error_reporting(E_ALL);
include_once 'koneksi.php';

if (isset($_POST['submit']))
{
    $id = $_POST['id'];
    $nama = $_POST['nama'];
    $kategori = $_POST['kategori'];
    $harga_jual = $_POST['harga_jual'];
    $harga_beli = $_POST['harga_beli'];
    $stok = $_POST['stok'];
    $file_gambar = $_FILES['file_gambar'];
    $gambar = null;
    
    if ($file_gambar['error'] == 0)
    {
        $filename = str_replace(' ', '_', $file_gambar['name']);
        $destination = dirname(__FILE__) . '/gambar/' . $filename;
        if (move_uploaded_file($file_gambar['tmp_name'], $destination))
        {
            $gambar = 'gambar/' . $filename;;
        }
    }
    
    $sql = 'UPDATE data_barang SET ';
    $sql .= "nama = '{$nama}', kategori = '{$kategori}', ";
    $sql .= "harga_jual = '{$harga_jual}', harga_beli = '{$harga_beli}', stok
    = '{$stok}' ";
    if (!empty($gambar))
        $sql .= ", gambar = '{$gambar}' ";
    $sql .= "WHERE id_barang = '{$id}'";
    $result = mysqli_query($conn, $sql);
    
    header('location: index.php');
}

$id = $_GET['id'];
$sql = "SELECT * FROM data_barang WHERE id_barang = '{$id}'";
$result = mysqli_query($conn, $sql);
if (!$result) {
    die('Error: Data tidak tersedia');
}
$data = mysqli_fetch_array($result);

function is_select($var, $val) {
    if ($var == $val) {
        return 'selected="selected"';
    }
    return false;
}

?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Ubah Barang</title>
</head>
<style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f7f7f7;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }

        .container {
            width: 400px;
            background: #fff;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            border-radius: 8px;
            overflow: hidden;
        }

        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 20px;
        }

        .main {
            padding: 20px;
        }

        .input {
            margin-bottom: 15px;
        }

        label {
            display: block;
            font-size: 14px;
            margin-bottom: 8px;
            color: #555;
        }

        input[type="text"],
        select,
        input[type="file"] {
            width: calc(100% - 16px);
            padding: 10px;
            box-sizing: border-box;
            margin-bottom: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-size: 14px;
        }

        select {
            width: 100%;
        }

        .submit {
            text-align: center;
        }

        input[type="submit"] {
            background-color: #4caf50;
            color: white;
            padding: 12px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.3s;
        }

        input[type="submit"]:hover {
            background-color: #45a049;
        }
    </style>
<body>
<div class="container">
    <h1>Ubah Barang</h1>
    <div class="main">
        <form method="post" action="ubah.php" enctype="multipart/form-data">
        <div class="input">
            <label>Nama Barang</label>
            <input type="text" name="nama" value="<?php echo
            $data['nama'];?>" />
            </div>
            <div class="input">
                <label>Kategori</label>
                <select name="kategori">
                <option <?php echo is_select
('Komputer', $data['kategori']);?> value="Komputer">Komputer</option>
<option <?php echo is_select
('Komputer', $data['kategori']);?> value="Elektronik">Elektronik</option>
<option <?php echo is_select
('Komputer', $data['kategori']);?> value="Hand Phone">Hand Phone</option>
</select>
</div>
<div class="input">
    <label>Harga Jual</label>
    <input type="text" name="harga_jual" value="<?php echo
    $data['harga_jual'];?>" />
    </div>
    <div class="input">
        <label>Harga Beli</label>
        <input type="text" name="harga_beli" value="<?php echo
        $data['harga_beli'];?>" />
        </div>
        <div class="input">
            <label>Stok</label>
            <input type="text" name="stok" value="<?php echo
            $data['stok'];?>" />
            </div>
            <div class="input">
                <label>File Gambar</label>
                <input type="file" name="file_gambar" />
            </div>
            <div class="submit">
                <input type="hidden" name="id" value="<?php echo
                $data['id_barang'];?>" />
                <input type="submit" name="submit" value="Simpan" />
            </div>
        </form>
    </div>
</div>
</body>
</html>
```

![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/73b055a7-7df4-4f17-9552-a32c7b176a66)

## Menghapus Data (Delete)
Buat file baru dengan nama hapus.php

```
<?php
include_once 'koneksi.php';
$id = $_GET['id'];
$sql = "DELETE FROM data_barang WHERE id_barang = '{$id}'";
$result = mysqli_query($conn, $sql);
header('location: index.php');
?>
```

### Before

![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/ebd744a0-0634-4446-9079-475df7eab950)

### After

![image](https://github.com/Aliyahasmarani/Lab8Web/assets/115197672/b49f1a92-0f2e-4d69-bde4-7b60c63779db)






