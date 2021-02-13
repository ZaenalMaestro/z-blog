---
title: "5. Express - Template Engine"
date: 2021-02-13T10:42:00+08:00
draft: false
author: "Zaenal"
categories: 
- express js
tags:
- expressjs
- javascript
- nodejs
---

Template engine adalah template untuk membuat view atau tampilan yang dapat menampilkan data secara
dinamis

### install view engine
terdapat beberapa view engine yang tersedia untuk express js seperti `pug`, `ejs`, `jade`, dan `hadlebaars` tapi saya lebih memilih handlebars karena sytax yang menurut saya sangat rapih

untuk menginstall view engine handlebars menggunakan perintah berikut 
```bash
npm install express-handlebars
``` 
atau bisa menggunakan _shortcut_ npm 
```bash
npm i express-handlebars
```

### setup view engine
Buat file `index.js`, dan directory `views`. 
Kemudian didalam  directory `views` buat directory `layouts`, dan file `index.handlebars`, didalam directory `layouts` buat file `main.handlebars`. Struktur foldernya seperti ini

{{<image src="/expressjs/tengine-structur-folder.PNG" alt="image">}}

buka file `index.js`, tambahkan script berikut
```javascript
// import modules
const express = require('express'),
   handlebars = require('express-handlebars'),
   path = require('path'),
   app = express()

// helper function
function directory(dir) {
   return path.join(__dirname, dir)
}

// set views directory
app.set('views', directory('views'))

// set view engine
app.set('view engine', 'handlebars')

// set view engine handlebars
app.engine('handlebars', handlebars({}))

// route
app.get('/', function (req, res) {
   res.render('index')
})

app.listen(3000, () => console.log('running on port 3000'))

```
kemudian buka file `main.handlebars` didalam directory `views/layouts`, tambahkan script berikut
```html
<!DOCTYPE html>
<html lang="en">
<head>
   <title>Main Layouts</title>
</head>
<body>
   {{{body}}}
</body>
</html>
```
`main.handlebars` ini berfungsi sebagai template utama kita. Perhatikan scirpt 
```html
{{{body}}}
```
saat kita mengakses file `view` semua script `views` akan diload dalam `{{{body}}}`

selanjutnya buka file `index.handlebars`, tambahkan script berikut
```html
<h1>Home Page</h1>
```
ya hanya tag `h1` dengan teks `Home Page`, yang nantinya ketika kita mengakses halaman `index.handlebars`, maka akan menggunakan `main.handlebars` sebagai templatenya maka hasilnya seperti ini
```html
<!DOCTYPE html>
<html lang="en">
<head>
   <title>Main Layouts</title>
</head>
<body>
   <h1>Home Page</h1>
</body>
</html>
```

jalankan server, kemudian buka browser dan silahkan akses url `localhost:3000` maka hasilnya sebagai berikut
{{<image src="/expressjs/tengine-layouts.PNG" alt="image">}}

### penjelasan source code index.js
```javascript
// import modules
const express = require('express'),
   handlebars = require('express-handlebars'),
```
seperti pada umumya untuk menggunaka package, kita harus meng`import` module express-handlebars supaya bisa digunakan

```javascript
app.set('view engine', 'handlebars')
```
pada bagian  ini kita set `handlebars` sebagai template engine yang kita gunakan 

```javascript
app.set('views', directory('views'))
```
kita mengeset directory `views` sebagai lokasi menyimpan semua file views `handlebars`

```javascript
app.engine('handlebars', handlebars({}))
```
pada bagian ini digunakan sebagai pengaturan template engine handlebars, kemudian `handlebars({...})` nantinya digunakan untuk melakukan konfigurasi seperti merubah ektensi file


```javascript
res.render('index')
```
berfungsi untuk menampilkan view `index.handlebars`


### ubah extensi file handlebars
untuk mengubah ekstensi handlebars menjadi lebih singkat, cukup dengan mengubah `view engine` seperti berikut
```javascript
app.set('view engine', 'hbs')
```
dan menambahkan propery object
```javascript
extname: 'hbs'
```
 berikut contoh lengkapnya
```javascript
// view engine
app.set('view engine', 'hbs')

// handlebars setup
app.engine('hbs', handlebars({
   extname: 'hbs'
}))
```
terakhir, ubah semua extensi file `.handlebars` menjadi `.hbs`

### Ubah directory layout
untuk mengubah directory layout tambahkan property object handlebars berikut 
```javascript
app.engine('hbs', handlebars({
   extname: 'hbs',
   layoutsDir: `${__dirname}/views/nama_directory`
}))
```

### Ubah file layout default
untuk mengubah file layout default tambahkan property object handlebars berikut
```javascript
defaultLayout:'layout'
```

lalu bagaimana jika kita ingin menggunakan layout lain, buat file layout baru didalam directory `layouts`

buat file layouts baru didalam directory `views/layouts` dengan nama `layoutBaru.hbs` dan ketik code berikut
```html
<!DOCTYPE html>
<html lang="en">

<head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <meta http-equiv="X-UA-Compatible" content="ie=edge">
   <title>{{title}}</title>
</head>

<body>
   <h1>ini adalah layout baru</h1>
   {{{body}}}
</body>

</html>
```
kemudia buka file `index.js` dan tambahkan property
```javascript
layout: 'layoutBaru'
```
 didalam route sebagai berikut
```javascript
// route
app.get('/', function (req, res) {
   res.render('index', {
      layout: 'layoutBaru'
   })
})
```
sekarang file `index.hbs` menggunakan `layoutBaru.hbs` sebagai templatenya

### membuat template partial
template partial yaitu teknik untuk membagi komponen komponen view kedalam file terpisah, ini berguna membuat komponen view menjadi modular sehingga mempermudah kita menghadle setiap komponen yang kita bangun

berikut contoh penggunaan partial, idenya kita buat komponen header dan footer secara terpisah. Didalam directory `views` buat directory `partials`, selanjutnya didalam folder `partial` buat file `header.hbs` dan `footer.hbs`. Sehingga stukturnya sebagai berikut

{{<image src="/expressjs/tengine-partials.PNG" alt="image">}}


pada file `header.hbs` tambahkan script berikut
```html
<!DOCTYPE html>
<html lang="en">

<head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <meta http-equiv="X-UA-Compatible" content="ie=edge">
   <title>Main Layouts</title>
</head>

<body>
```
selanjutnya difile `footer.hbs` dan tambahkan script berikut
```html
<footer>@ footer </footer>
```
kemudian buka file `main.hbs` dan panggil file partial yang telah dibuat berikut ini dengan menambahkan scirpt
```html
{{> nama_partial}}
``` 
hasilnya seperti berikut
```html
<!DOCTYPE html>
<html lang="en">
{{> header }}
<body>
{{{body}}}   
</body>
{{> footer }}
</html>
```
selanjutnya buka file `index.js` dan tambahkan property `partialDir` untuk menentukan path directory partial
```javascript
// handlebars
app.engine('hbs', handlebars({
   extname: 'hbs',
   partialsDir: `${__dirname}/views/partials`
}))

// route
app.get('/', function (req, res) {
   res.render('index')
})
```
ketika kita akses url `http://localhost:3000/` maka hasilnya sebagai berikut
{{<image src="/expressjs/tengine-outputpartials.PNG" alt="image">}}
kita telah berhasil memanggil template partial yang telah kita buat

### menampilkan data di handlebars
Buka file `index.js` tambahkan script berikut
```javascript
const biodata = {
   nama: 'Zaenal',
   alamat: 'Makassar'
}
// route
app.get('/', function (req, res) {
   res.render('index', {
      judul: 'Biodata',
      dataSaya: biodata
   })
})
```
kemudian buka file `index.hbs` tulis kode berikut
```html
<h1>Home Page</h1>

<h3>{{judul}}</h3>
<ul>
   <li>{{dataSaya.nama}}</li>
   <li>{{dataSaya.alamat}}</li>
</ul>
```
hasilnya sebagai berikut
{{<image src="/expressjs/tengine-injectdata.PNG" alt="image">}}

### perulangan
berikut cara membuat perulangan dihandlebars,
contoh kita memiliki object yang akan ditampilkan dibrowser:
```javascript
{
   nama: [
      'Zaenal',
      'wahyuni',
      'noufal'
   ]
}
``` 
untuk 
```html
<ul>
   {{#each nama}}
      <li>{{this}}</li>
   {{/each}}
</ul>
```
maka hasilnya akan menapilkan semua daftar nama

 untuk dokumentasi lengkapnya silahkan kunjungi [handlebarsjs](https://handlebarsjs.com/) 
 
 cukup sekian pembahasan tentang view engine handlebars semoga bermanfaat.


