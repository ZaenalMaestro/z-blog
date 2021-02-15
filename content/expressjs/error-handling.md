---
title: "6. Express - Error Handling"
date: 2021-02-15T20:37:06+08:00
draft: false
author: "Zaenal"
categories: 
- express js
tags:
- expressjs
- javascript
- nodejs
---

Express telah menyediakan error handling(penangan error) bawaan. Express menggunakan middleware untuk melakukan error handling, cara untuk membuat `middleware error handling` sama seperti membuat `function middleware`, tetapi menggunakan empat parameter yaitu `err`, `req`, `res`, `next`. 

### membuat middleware error handling 
Saat membuatnya, Pastikkan letaknya berada di bagian paling bawah dari semua middleware yang kita buat, agar dapat menangani error yang ada, contonya sebagai berikut
```javascript
// route
app.get('/', (req, res, next) => {
  const err = new Error('Something went wrong !')
  next(err)
})

// middleware
app.use((err, req, res, next) => {
  res.status(500).send('Oops, Terjadi kesalahan !')
})
```
selanjutnya kita buka `localhost:3000`, hasilnya sebagai berikut:
{{<image src="/expressjs/error-tampilan.PNG" alt="image">}}

### Error handling pada fungsi asyncronous
Untuk Error handling pada fungsi asyncronous, kita menggunakan fungsi `next()` untuk meneruskan error ke middleware,
contohnya sebagai berikut:
```javascript
// route
app.get('/home', (req, res, next) => {
  // read file
  fs.readFile('index.htmx', (err, data) => {
    if(err) return next(err)
    res.send(data)
  })
})

// middleware error
app.use((err, req, res, next) => {
  res.status(500).send('Oops, Terjadi kesalahan !')
})
```
pada contoh diatas kita membaca file dengan ekstensi file yang salah `index.htmx`, sehingga terjadi error,
ketika kita mengakses `localhost:3000/home` tampilannya sebagai berikut
{{<image src="/expressjs/error-async.PNG" alt="image">}}

### Error handling pada kode asyncronous
Untuk menangani error pada kode asyncronous kita harus menambahkan blok `try..catch`, kemudian pada blok `catch` kita menjalankan fungsi `next(err)` untuk mengirimkan pesan errornya ke middleware. Contohnya sebagai berikut
```javascript
app.get('/', (req, res, next) => {
  setInterval(() => {
    try {
      throw new Error('Terjadi error !!!')
    } catch (err) {
      next(err)
    }
  }, 1000);
})

// middleware error
app.use((err, req, res, next) => {
  res.status(500).send('Oops, Terjadi kesalahan !')
})
```
selanjutnya kita akses `localhost:3000/`, maka akan menampilkan response dari middleware error handling. Hasilnya sebagai berikut
{{<image src="/expressjs/error-code-async.PNG" alt="image">}}
tetapi jika kita menghilangkan blok try..catch pada kode asycronous
```javascript
app.get('/', (req, res, next) => {
  setInterval(() => {
    throw new Error('Terjadi error !!!')
  }, 1000);
})
```
maka express tidak dapat menangkap error yang terjadi.

sumber referensi
- [expressjs.com - error handling](https://expressjs.com/en/guide/error-handling.html)
- [tutorialspoint - express JS error handling](https://www.tutorialspoint.com/expressjs/**expressjs_error_handling**.html)

Cukup sekian pembahasa tentang Error handling di Express. jika ada pertanyaan silahkan tulis dikolom komentar 😁