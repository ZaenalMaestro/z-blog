---
title: "8. Express - Session"
date: 2021-02-18T13:02:04+08:00
draft: false
author: "Zaenal"
categories: 
- express js
tags:
- expressjs
- javascript
- nodejs
---
Session merupakan salah satu fitur yang penting pada aplikasi web, dengan menggunakan session kita dapat melacak aktivitas user, misalnya kita dapat mengetahui apakah user telah login atau belum, untuk menggunakan session kita bisa mendownload package `express-session`, berikut ini co

### pengaturan session
```javascript
app.use(session({
  secret: 'Secret-key',
  saveUninitialized: false,
  resave: false,
  cookie: {
    maxAge: 60000
  }
}))
```
untuk menggunakan session pertama kita harus mendaftarkanya session di middleware menggunakan `app.use()`

didalam session terdapat beberapa `property`, berikut penjelasannya:
### secret
berfungsi untuk kerahasiaan session session bertujuan untuk mencegah gangguan yang tidak diinginkan,

### saveUninitialized
berfungsi untuk menyimpan session object yang tidak diinisalisasi (session kosong yang baru saja dibuat) kedalam session store (penyimpanan session diserver) jika kita beri nilai `true`

tetapi dari dokumentasinya disarankan set nilainya `false` untuk mencegah pengguan session store secara berlebih 

### resave
berfungsi untuk menyimpan perubahan apabila kita memodifikasi object session, misalnya kita menambahkan session login, hanya akan tersimpan di `session story` apabila kita memberi nilai `resave: "true"`, jika diberi `false` sessin login tidak akan tersimpan

### maxAge
untuk mengatur batas waktu penggunaan session kita bisa menambahkan nilai dalam satuan `milisecond`, pada contoh diatas saya set nilainya `60000` milisecond sehingga session akan berakhir dalam waktu 1 menit.

Selanjutnya mulai belajar bagaimana menggunakan session, pertama buat directory baru dengan nama `belajar-session`, kemudian didalam directorynya kita inisaliasi aplikasi yang akan kita bangun dengan mengetikkan perintah berikut
```bash
npm init -y
```

selanjutnya kita install express dan express-session, dengan mengetikkan perintah berikut
diterminal
```bash
npm i express express-session
```

selanjutnya buat file server.js dan tambahkan kode berikut
```javascript
const express = require('express')
const session = require('express-session')
const app = express()

app.use(session({
  secret: 'Secret-key',
  saveUninitialized: false,
  resave: false,
  cookie: {
    maxAge: 60000
  }
}))

app.get('/login', (req, res) => {
  req.session.login = true
  res.send('Login berhasil')
})

app.get('/logout', (req, res) => {
  req.session.destroy((err) => {
    if (err) throw err
    res.send('logout berhasil')
  })
})

app.get('/dashboard', (req, res) => {
  if (req.session.login) return res.send('Selamat Datang didashboard!')
  res.send('Anda belum login')
})

app.get('/tes', (req, res) => {
  console.log(req.session)
})

app.listen(3000, () => {
  console.log('Server running on http://localhost:3000')
})
```
perhatikan pada route `/login` kita menambahkan object session baru yaitu session login
```javascript
req.session.login = true
```
sehingga kita dapat menggunakannya secara global

kemudian pada route `/dashboard` kita melakukan pengecekan jika session login
bernilai `true` kirimkan response ke browser "Selamat Datang didashboard!", selain itu akan menampilkan response "Anda belum login"

selamjutnya pada route `/logout` kita memusnahkan session, jadi ketika telah logout, kita tidak bisa lagi masuk kedashboard, kecuali kita melakukan login kembali

cukup sekian artikel tentang express session. semoga memberikan manfaat bagi pembaca.

