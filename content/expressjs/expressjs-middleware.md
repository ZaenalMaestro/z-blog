---
title: "Express js - Middleware"
date: 2021-02-10T08:21:16+08:00
draft: false
author: "Zaenal"
categories: 
- express js
tags:
- expressjs
- javascript
- nodejs
---
Middleware merupakan `gerbang` antara request user dengan aplikasi, kalau dianalogikan dikehidupan nyata jika seseorangan ingin menonton pertandingan sepak bola distadion, untuk masuk kestadion harus melewati pintu gerbang, `gerbang` ini bisa kita anggap sebagai middlware

digerbang ini kita dapat melakukan banyak hal seperti pemeriksaan senjata tajam, maupun mengecek tiket penonton

nah itu contoh sederhana dari middleware dikehidupan nyata. Web Framerwork saat ini kebanyakan telah menyediakan middleware

### Middleware Express js
Middleware express js merupakan fungsi yang dapat mengakses semua object request `req` atau response `res`, midlware dapat menjalankan tugas berikut:
- menjalankan `function` apapun
- mengubah objeck `req` dan `res`
- mengakhiri request yang masuk
- meneruskan ke middleware yang lain

berikut ini cara membuat middleware
``` javascript
function namaFungsiMiddleware(req, res, next){
   ...
   next()
}
```
perhatikan argument `next`, argument ini merupakan `callback` yang berfungsi sebagai middleware, dengan menjalankan `next` kita dapat melanjutkan ke middleware yang lain

 jika kita tidak menjalankan callback `next` maka request akan berhenti di middleware, seperti analogi diawal jika callback `next` tidak dijalankan maka gerban stadion tidak akan dibuka sehingga penonton tidak bisa masuk

kita akan coba membuat fungsi middlware untuk mencatat penonton yang masuk, buat file `belajar-middleware.js`
```javascript
// import module express
const express = require('express')
const app = express()

// buat middleware
const catatPenontonMasuk = function (req, res, next) {
   console.log('Ada penonton yang masuk stadion...')
   next()
}

// route
app.get('/penonton', function(req, res) {
   res.send('Selamat datang distadion Gelora Bung karno')
})

// server berjalan diport 3000
app.listen(3000)
console.log('server berjalan diport http://localhost:3000')

```
buka terminal ketik `node belajar--middleware`

{{<image src="/expressjs/terminal.PNG" alt="terminal">}}


kemudian buka browser kemudian ketikkan di address bar `localhost:3000/penonton` maka hasilnya sebagai berikut

{{<image src="/expressjs/browser.PNG" alt="browser">}}

lihat kembali dibrowser akan tampil pesan yang telah kita buat dimiddleware

{{<image src="/expressjs/output-terminal.PNG" alt="output-terminal">}}

kita coba contoh lain,  membuat validasi dan atutentikasi pembelian tiket sepak bola dimidleware 

buat file `utentikasi.js` dan copy source code berikut

```javascript
// import module express
const express = require('express')
const app = express()

// autentikasi
const autentikasi = function (req, res, next) {
   const login = req.params
   if (login.username === 'zaenal' && login.password === '1234') {
      return next()
   }
   res.status(401).send('Username atau password tidak sesuai')
}

// route
app.get('/login/:username/:password', autentikasi, function (req, res) {
   res.send('halaman Dashboard')
})


app.listen(3000)
console.log('Server berjalan di http://localhost:3000')
```

jika username dan password sesuai akan menampikan `Halaman dashboard`

{{<image src="/expressjs/halaman-dashboard.PNG">}}

jika username atau password salah akan menampikan `Username atau password tidak sesuai`

{{<image src="/expressjs/gagal-login.PNG">}}

**penjelasan source code**
```javascript
// autentikasi
const autentikasi = function (req, res, next) {
   const login = req.params
   if (login.username === 'zaenal' && login.password === '1234') {
      return next()
   }
   res.status(401).send('Username atau password tidak sesuai')
}
```

kita buat fungsi untuk melakukan autentikasi dengan nama `autentikasi`, jika username dan password sesuai jalankan fungsi `next()` ini akan melanjutkan request ke`halaman dashboard`, keyword `return` berfungi agar tidak menjalankan kode dibawahnya, sehingga kita tidak perlu menabahkan kondisi `else`

jika username dan password tidak sesuai maka akan memberikan respons status `401 (Unauthorized)` dan menampilkan teks dibrowser `Username atau password tidak sesuai`

untuk penjelasan source code route
```javascript
// route
app.get('/login/:username/:password', autentikasi, function (req, res) {
   res.send('halaman dahsboard')
})
```

kita buat route dengan request method `GET`, dengan `url: /login/:username/:password` sebagai argument pertama yang akan menerima parameter `username` dan `password` 

kemudian parameter kedua kita pasang middleware `autentikasi` 

dan parameter ketiga adalah fungsi untuk menampilkan `halaman dashboard` ini hanya bisa dijalankan jika autentikasi berhasil di middleware

cukup sekian pembahasan awal tentang middleware, kita lanjut ke part 2 untuk membahasan materi middleware yang lebih lanjut

semoga bermanfaat.