---
title: "2. Express - Basic Routing"
date: 2021-02-11T12:01:46+08:00
draft: false
author: "Zaenal"
categories: 
- express js
tags:
- expressjs
- javascript
- nodejs
---

route berfungsi untuk manangani navigasi pengguna ke berbagai url diseluruh aplikasi kita atau biasa disebut `requst url`, untuk melakukan requst url biasanya menggunakan `http` (*Hypertext Transfer Protocol*) adalah protokol atau aturan berfungsi untuk mengkomunikasikan data dari server ke browser

terdapat banyak jenis http request, yang paling umum digunakan adalah `GET`, `POST`, `PUT`, dan `DELETE`
- `GET` biasanya digunakan untuk melakukan request melalui url
- `POST` digunakan untuk menambahkan data
- `PUT` merupakan jenis http request untuk mengubah data
- `DELETE` jenis request yang digunakan untuk menghapus data
  
### basic route
berikut ini contoh dasar pembuatan route
```javascript
const express = require('express'),
   app = express()

// basic route
app.get('/', (req, res) => {
   res.send('Belajar route')
})
```
pertama kita import module express, kemudian menginisalisis object `app`
pada basic route kita membuat route dengan request method `GET`, 
dan mengirimkan respon belajar route

### Menangani requset method
Selanjutnya kita akan mencoba membuat route untuk menangani `request url` dengan method `GET`, `POST`, `PUT`, dan `DELETE`

buat file bernama `index.js`, tambahkan script berikut:
```javascript
const express = require('express'),
   app = express()

app.get('/user', function (req, res) {
   res.send(`Request method: ${req.method}`)
})

app.post('/user', (req, res) => {
   res.send(`Request method: ${req.method}`)
})

app.put('/user', (req, res) => {
   res.send(`Request method: ${req.method}`)
})

app.delete('/user', (req, res) => {
   res.send(`Request method: ${req.method}`)
})

app.listen(3000)
console.log('Server running on http://localhost:3000')
```
ketika kita mengakses salah satu route diatas, maka akan menampilkan respones berdasarkan request method masing-masing route

jalankan server node di terminal menggunakan perintah
```bash
node index.js
```
pastikan kalian telah menginstall postman, aplikasi ini berfungsi menguji `http request` seperti methode `GET`, `POST`, `PUT`, `DELETE` dan seterusnya. 

Kita akan mencoba mengakses setiap route yang kita buat seperti yang terlihat pada gambar berikut:

-Request method `GET` dengan url `http://localhost:3000/user`
{{<image src="/expressjs/route-get.PNG" alt="browser">}}

-Request method `POST` dengan url `http://localhost:3000/user`
{{<image src="/expressjs/route-post.PNG" alt="route post">}}

-Request method `PUT` dengan url `http://localhost:3000/user`
{{<image src="/expressjs/route-put.PNG" alt="img">}}

-Request method `DELETE` dengan url `http://localhost:3000/user`
{{<image src="/expressjs/route-delete.PNG" alt="browser">}}

### route  parameter
router paramater adalah nama segment url yang digunakan untuk menangkap nilai yang spesifik. paramter ditandai dengan simbol `:` kemudian diikuti `nama parameter`, 
untuk menangkap nilai parameter menggunakan fungsi `req.params` 

Berikut ini adalah contoh route parameter
```javascript
app.get('/costumer/:idCostumer', (req, res) => {
   console.log(req.params)
})
```
ketika kita melakukan request url `http://localhost/costumer/3` dengan request method `GET`,hasilnya akan menampilkan object:
```javascript
{ idCostumer: '3' }
``` 

### app.router()
jenis route ini digunakan apabila kita akan melakukan `http request` di `route path` yang sama 
dengan request method yang berbeda, contohnya sebagai berikut:
```javascript
app.route('/users')
   .get((req, res) => {
      res.send('Menampilkan data user')
   })
   .post((req, res) => {
      res.send('Menambahkan data user')
   })
   .put((req, res) => {
      res.send('Edit data user')
   })
   .delete((req, res) => {
      res.send('Menghapus data user')
   })
```
cukup dengan membuat satu route path `/user`, 
kita sudah bisa melakukan request menggunakan method `GET`, `POST`, `PUT`, `DELETE`

