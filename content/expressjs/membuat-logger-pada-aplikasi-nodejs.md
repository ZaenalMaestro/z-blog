---
title: "Membuat Logger Pada Aplikasi Node js"
date: 2021-03-01T17:21:24+08:00
draft: false
author: "Zaenal"
categories: 
- express js
tags:
- expressjs
- javascript
- nodejs
---

Logger adalah salah satu cara untuk mencatat informasi aplikasi kita

misalnya mencatat request masuk, mencatat aktifitas user, ataupun mencatat error yang terjadi 

semakin detail log yang kita buat semakin bagus, 

tetapi perlu diingat, jangan mencatat informasi sensitif kedalam log 

seperti password, data pribadi user, atau nomor kartu kredit 

terdapat beberapa level log yang perlu kita ketahui
- `Info`: untuk mencatat semua informasi aktifitas aplikasi
  
- `Warning`: untuk mencatat informasi yang mungkin akan menjadi error
  
- `Error`: untuk mencatat error yang terjadi pada aplikasi
  
- `Debug`: biasa digunakan  untuk mencatat informasi saat kita melakukan pengembangan
  
- `fatal`: untuk mencatat masalah serius yang terjadi pada aplikasi

terdapat beberapa logger yang populer digunakan seperti winston, log4js, bunyan 

penulis sendiri menggunakan winston untuk membuat logger

dengan menggunakan winston kita bisa menyimpan logger kedalam console, file, ataupun kedalam database

#### membuat logger
kita akan membuat contoh logger untuk mencatat user yang melakukan login kedalam file

pertama buat directory baru dengan nama `logger`

kemudian didalam directory yang baru kita buat, ketik perintah npm
```bash
npm init -y
```
selanjutnya install package `express` dan `winston`
```bash
npm i express winston
```

selanjutnya buat file baru dengan nama `app.js` dan tambahkan kode berikut
```javascript
const express = require("express")
const app = express()

app.use(express.urlencoded({extended: false}))

app.post('/login', (req, res) => {
  res.send("berhasil login")
})

app.listen(3000, () => {
  console.log("server running on port: 3000")
})
``` 

selanjutnya buat file baru dengan nama `logger.js` dan tambahkan kode berikut
```javascript
const winston = require("winston")

const logger = winston.createLogger({
  transports: [
    new winston.transports.File({
      filename: "info.log",
      level: "info",
      format: winston.format.json()
    })
  ]
})

module.exports = logger
```
pada kode diatas kita membuat konfigurasi logger 

dimana kita membuat logger dalam bentuk file, dengan nama `info.log`

file `info.log` sendiri secara otomatis akan dibuat saat logger pertama kali
mencatat informasi pada aplikasi

untuk format penulisan logger didalam file `info.log` menggunakan format penulisan  json

logger yang kita buat akan mencatat informasi pada level `info`

selanjutnya import logger yang telah kita buat kedalam `app.js`
```javascript
const express = require("express")
const app = express()
const logger = require("./logger")
```

kemudian kita menggunakan logger didalam route `/login` untuk mencatat informasi user yang melakukan login

tambahkan kode berikut didalam route `/login`
```javascript
app.post('/login', (req, res) => {
  const {email} = req.body
  logger.log("info", `${email} berhasil login`)
  res.send("berhasil login")
})
```

berikut kode lengkapnya pada file `app.js` 
```javascript
const express = require("express")
const app = express()
const logger = require("./logger")

app.use(express.urlencoded({extended: false}))

app.post('/login', (req, res) => {
  const {email} = req.body
  logger.log("info", `${email} melakukan login`)

  res.send("berhasil login")
})

app.listen(3000, () => {
  console.log("server running on port: 3000")
})
```

perhatikan logger pada route `/login`
```javascript
logger.log("info", `${email} melakukan login`)
```
artinya kita akan mencatat logger informasi user yang login pada level `info`

selanjutnya jalankan server

kemudian buka aplikasi postman, dan akses URL `http://localhost/login/` 

menggunakan method `post` dengan mengirimkan `request body` email dan password
{{<image src="/expressjs/logger.PNG" alt="image">}}

setelah melakukan request, kemudian cek file `info.log` yang dibuat secara otomatis 

hasilnya seperti berikut
```log
{"level":"info","message":"user@gmail.com melakukan login"}
```
kita telah berhasil membuat logger

Cukup sekian artikel kali ini, semoga memberikan manfaat bagi pembaca, 

jika ada kritik, saran, atau pertanyaan silahkan tulis dikolom komentar