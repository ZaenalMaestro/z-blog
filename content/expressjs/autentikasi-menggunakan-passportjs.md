---
title: "Express - Autentikasi Menggunakan Passportjs"
date: 2021-02-22T08:11:19+08:00
draft: false
author: "Zaenal"
categories: 
- express js
tags:
- expressjs
- javascript
- nodejs
- passport js
---

Passport js merupakan sistem autentikasi sederhana yang dapat melakukan autentikasi menggunakan username dan password, facebook, twitter, google dan lain-lain, untuk melihat autentikasi apa saja yang bisa digunakan, silahkan kujungi [passport.js](http://passportjs.com)

### install package
di artikle ini kita akan melakukan autentikesi menggunakan username dan password, berikut ini package yang dibutuhkan:
- passport
- passport-local
- ejs
- express
- express-flash
- express-session

pertama initialisasi aplikasi yang kita buat dengan menggunakan perintah:
```bash
npm init -y
```

selanjutnya download package yang dibutuhkan dengan mengetikkan perintah npm berikut di terminal
```bash
npm i passport passport-local ejs express express-flash express-session
```

berikut ini versi dari package yang saya download
```json
{
  "name": "materi-autentikasi",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "nodemon": "^2.0.7"
  },
  "dependencies": {
    "ejs": "^3.1.6",
    "express": "^4.17.1",
    "express-flash": "0.0.2",
    "express-session": "^1.17.1",
    "passport": "^0.4.1",
    "passport-local": "^1.0.0"
  }
}
```

### membuat server express
selanjutnya buat file `index.js` dan tambahkan kode berikut
```javascript
const express = require('express')
const app = express()
const session = require('express-session')
const flash = require('express-flash')
const passport = require('passport')

// set up view engine
app.set('view engine', 'ejs')

// untuk memparsing data dengan requst method POST
app.use(express.urlencoded({extended: false}))

// insialisasi express flash
app.use(flash())

// inisialsisasi session
app.use(session({
  secret: 'top-secret',
  saveUninitialized: false,
  resave: false
}))

// inisialisasi passport js
app.use(passport.initialize())
app.use(passport.session())

// route

// server berjalan diport 3000 
app.listen(3000, () => console.log('server running on port: 3000'))
```

### membuat daftar user
selanjutnya buat file `user.js` untuk membuat data user dan tambahkan kode berikut
```javascript
// daftar user
const users = [
  {
    id: '1',
    name: 'User One',
    username: 'user_1',
    password: '1234'
  },
  {
    id: '2',
    name: 'User Two',
    username: 'user_2',
    password: '4321'
  }
]

// fungsi untuk menampikan user berdasarkan id
const findUserById = (id) => {
  return users.find(user => user.id === id)
}

// fungsi untuk menampikan user berdasarkan username
const findUserByUsername = (username) => {
  return users.find(user => user.username === username)
}

// export module
module.exports = {findUserById, findUserByUsername}
```

### configurasi passport js
selanjutnya buat file `config-passport.js` dan tambahkan kode berikut
```javascript
const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy
// import module dari user.js
const userModel = require('./user')

// fungsi autentikasi yang akan menjadi callback dari LocalStrategy 
const authenticate = (username, password, done) => {
  // mendapatkan data user berdasarkan username
  const user = userModel.findUserByUsername(username)

  // jika username tidak ada didaftar user
  if (!user) return done(null, false, { message: 'Username tidak terdaftar !' })

  // jika passpord yang dinput salah
  else if (password !== user.password) return done(null, false, { message: 'Password salah !' })

  // user berhasi diautentikasi
  return done(null, user)
}

// membuat autentikasi menggunakan username dan password (passport local-strategy)
passport.use(new LocalStrategy(authenticate))

// menyimpan id user yang telah diatentikasi kedalam session
passport.serializeUser((user, done) => done(null, user.id))

/* 
menyimpan data user yang berhasil di autentikasi kedalam session cookie
- untuk mendapatkan data user, menggunakan req.user
*/ 
passport.deserializeUser((id, done) => done(null, userModel.findUserById(id)))
```

### buat views
selanjutnya buat directory baru dengan nama `views`, dalamanya buat dua file yaitu `index.ejs` dan `login.ejs`

pada file `login.ejs` tambahkan kode berikut:
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <title>Document</title>
</head>

<body>
<% if(messages.error) {%>
  <%= messages.error %>
<%}%>
<h1>Form Login</h1>
<form action="/login" method="post">
  <input type="text" name="username" placeholder="Input username..."><br>
  <input type="password" name="password" placeholder="Input password..."><br>
  <button type="submit">Login</button>
</form>
</body>
</html>
``` 
perhatikan potongan kode berikut
```html
<% if(messages.error) {%>
  <%= messages.error %>
<%}%>
```
kode ini akan menampilkan flash message yang telah dibuat pada file `config-passport.js` jika terjadi user gagal diautentikasi

pada file `index.js` tambahkan kode berikut
```html
<h1>Welcome <%=name%></h1>
```
`<%=name>` akan menampilakan nama user yang berhasil login

### memambahkan autentikasi pada route
selanjutnya buka kembali file `index.js` dan tambakan kode berikut dibawah komentar `// route`
```javascript
app.get('/login', (req, res) => {
  res.render('login')
})

app.post('/login', passport.authenticate('local',{
  successRedirect: '/',
  failureRedirect: '/login',
  failureFlash: true
}))


app.get('/', (req, res) => {
  res.render('index', {
    name: req.user.name
  })
})
```

perhatikan kode berikut ini 
```javascript
app.post('/login', passport.authenticate('local', {
  successRedirect: '/',
  failureRedirect: '/login',
  failureFlash: true
}))
```
pada potongan kode kita diatas kita akan melakukan autentikasi user yang akan login,
jika berhasil diautentikasi maka akan diarahkan kehalaman `index` jika gagal akan kembalikan kehalaman login, 

untuk `failureFlash: true` berfungsi menampikan pesan kesalahan yang telah kita buat pada file `config-passport.js`

sekarang jalankan server, dan akses `url: http://localhost:3000/login`, maka akan menampilkan form login
{{<image src="/expressjs/auth-form-login.PNG" alt="image">}}

jika kita input username atau password yang salah, akan menampilkan pesan kesalahan berikut
{{<image src="/expressjs/auth-username-salah.PNG" alt="image">}}

{{<image src="/expressjs/auth-password-salah.PNG" alt="image">}}

jika berhasil diautentikasi, akan diarahkan kehalaman `index`
{{<image src="/expressjs/auth-index.PNG" alt="image">}}

### membuat meddleware untuk cek status autentikasi
agar user tidak dapat mengakses halaman index jika belum login, kita buat middleware, tambahkan middlewre berikut file `index.js`
```javascript
function checkAuthenticated(req, res, next) {
  // jika telah diautentikasi lanjutkan ke halaman index
  if (req.isAuthenticated()) return next()
  // jika telah diautentikasi kembalikasi ke halaman login
  res.redirect('/login')
}

function checkNotAuthenticated(req, res, next) {
  // jika telah diautentikasi kembalikan ke halaman index
  if (req.isAuthenticated()) return res.redirect('/')

  // jika belum diautentikasi arahkan kehalaman login
  next()
}

app.listen(3000, () => console.log('server running on port: 3000'))
```

kemudian tambahkan middleware disetiap route
```javascript
app.get('/login', checkNotAuthenticated, (req, res) => {
  res.render('login')
})

app.post('/login', passport.authenticate('local',{
  successRedirect: '/',
  failureRedirect: '/login',
  failureFlash: true
}))


app.get('/',checkNotAuthenticated, (req, res) => {
  res.render('index', {
    name: req.user.name
  })
})
```

### buat link logout
buka file `index.js` tambahkan route berikut
```javascript
app.get('/logout',checkNotAuthenticated, (req, res) => {
  req.logOut()
  res.redirect('/login')
})

kemudian buka file `index.ejs` tambahkan kode berikut
```html
<h1>Welcome <%= name %></h1>
<a href="/logout">Log Out</a>
```

cukup sekian pembahasan tentang autentikasi menggunakan passport js, dan semoga bermanfaat.








