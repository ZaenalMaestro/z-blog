---
title: "Express - Integrasi Database menggunakan MySQL"
date: 2021-02-16T19:31:55+08:00
draft: false
author: "Zaenal"
categories: 
- express js
tags:
- expressjs
- javascript
- nodejs
---

Ada banyak pilihan database yang bisa digunakan untuk framework express seperti MySQL, MongoDb, Casandra dan lain-lain, tapi diartikle kali ini kita hanya akan menggunakan database MySQL

Untuk melakukan koneksi kedatabase kita akan menggunakan metode `database pooling`, dengan menggunakan teknik ini akan mempertahan koneksi kedatabase tetap terbuka sehingga dapat digunakan kembali ketika ada request baru kedatabase, 

kenapa kita menggunakan `database pooling`, yaitu untuk mengurangi penggunaan resource memory saat membuka koneksi kedatabase 

bayangkan jika ada request kedatabase aplikasi kita membuka koneksi baru, misalkan untuk membuat koneksi ke database memerlukan memory `1 Mb` 

jika ada `10.000 request` secara bersamaan kedatabase maka akan membuka `10.000 connection` baru, berarti memerlukan `memory 10 Gb` hanya untuk membuka koneksi

ini merupakan pemborosan resource memory, oleh karena itu sangat disarankan menggunakan `database pooling`

### Buat koneksi ke databae
pertama kita buat directory baru dengan nama `database-integration`, setelah membuat directory baru, didalamnya kita buat file `package.json` menggunakan perintah `npm`
```bash
npm init -y
```
selanjutnya kita install `express` dan `mysql` dengan mengetikkan perintah `npm` berikut
```bash
npm i express mysql
```
setelah proses install selesai, kita cek file `package.json`
```json
{
  "name": "database-integration",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.17.1",
    "mysql": "^2.18.1"
  }
}
```

selanjutnya buat database dengan nama `test` yang didalamnya terdapat tabel `user`, contohnya seperti berikut ini
{{<image src="/expressjs/database-dbtest.PNG" >}}

kemudian buat file `database.js` dan tambahkan kode berikut
```javascript
const mysql = require('mysql')

const pool = mysql.createPool({
  host: 'localhost',
  user: 'root',
  password: '',
  database: 'test',
  connectionLimit: 10
})

module.exports = pool
```
jika kalian memiliki password database silahkan diisi.

Perhatikan pada atribute `connectionLimit: 10` artinya aplikasi hanya membuka 10 koneksi 

ketika ada `20 request`, maka `request ke-11` sampai `20` akan mengantri untuk mendapatkan koneksi kedatabase, 

jika `10 request` pertama telah selesai, koneksi akan dikembalikan ke `database pool`, selanjutnya `database pool` akan memberikan koneksi yang tersedia ke `request ke-11` sampai `20`

selanjutnya buat file `index.js`, tambahkan kode berikut
```javascript
const express = require('express'),
  dbPool = require('./database'),

  app = express()

app.use(express.json())

app.get('/', (req, res) => {
  dbPool.query('SELECT * FROM user', (err, result) => {
    if (err) throw err
    res.status(200).json(result)
  })
})

app.listen(3000, () => {
  console.log('Server running on http://localhost:3000')
})
```
selanjutnya kita buka url `http://localhost:3000`, hasilnya akan menampilkan data json dibrowser
{{<image src="/expressjs/database-json.PNG" alt="image">}}

berikut penjelasan kode pada file index.js
```javascript
const express = require('express'),
  dbPool = require('./database')
```
pertama kita mengimport `database pooling` dari file `database.js` untuk melakukan koneksi kedatabase

```javascript
app.use(express.json())
```
pada potongan kode diatas, kita set pada middleware agar dapat menampilkan data dalam bentuk json

```javascript
app.get('/', (req, res) => {
  dbPool.query('SELECT * FROM user', (err, result) => {
    if (err) throw err
    res.status(200).json(result)
  })
})
```
pada potongan kode diatas kita membuat route yang didalamnya kita melakukan query kedatabase untuk menampilkan semua data dari tabel user

kemudian hasilnya akan ditampilkan dibrowser dalam format` JSON` dengam `res status: 200`

Cukup sekian pembahasan tentang integarasi database Express. dan semoga artikel ini memberikan manfaat bagi pembaca 😁.