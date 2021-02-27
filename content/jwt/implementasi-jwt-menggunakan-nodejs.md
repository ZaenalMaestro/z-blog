---
title: "Part 2 - Implementasi JSON Web Token Menggunakan Node Js"
date: 2021-02-27T10:06:07+08:00
draft: false
author: "Zaenal"
categories: 
- JWT
tags:
- jwt
- json
---

pada artikel sebelummnya penulis telah menjelaskan tentang konsep JSON Web Token

jika belum membacanya silahkan kunjungi [PART 1 - Konsep JSON Web Token](/jwt/konsep-jwt/)

untuk menggunakan JSON Web Token kita bisa mendownload package `jsonwebtoken`

ada dua hal penting dalam menerapkan JSON Web Token di node js yaitu jwt.sign() dan `jwt.verify()`
#### jwt.sign()
`jwt.sign() `digunakan untuk membuat JSON web token
```javascript
jwt.sign({data: username}, 'secret-key', {expiresIn: "1h"}, (err, token) => {
  if (err) res.json({ message: err})
  else res.json({ accessToken: token })
})
```
`jwt.sign()` parameter pertamanya yaitu payload yang merupakan object literal
```javascript
{data: username}
```

kemudian parameter kedua adalah `key` yang digunakan untuk membuat `signature`

pada parameter ketiga merupakan pengaturan JSON web token seperti batas kadaluarsa token menggunakan object
```javascript
{expiresIn: "1h"}
```

dan parameter terakhir adalah callback untuk mendapatkan token
```javascript
jwt.sign(<payload>, <key>, <options>, (err, token) => {
  if (err) res.json({ message: err})
  else res.json({ accessToken: token })
})
```


#### jwt.verify()
`jwt.verify()` digunakan untuk menverifikasi token yang dikirimkan oleh user, fungsi ini dibuat didalam fungsi middleware

```javascript
jwt.verify(token, 'secret-key', (err, decoded) => {
  if (err) res.json({ message: err})
  else next()
})
```

parameter pertamanya yaitu berisi token yang dikirimkan oleh user

kemudian parameter kedua yaitu `key` yang sama dengan `key` `jwt.sign()`

paramater ketiga yaitu callback yang akan menjalankan fungsi `next()` jika token valid

perhatikan parameter encoded pada callback, encoded berisi nilai payload 

### contoh penggunaan JSON web Token
pertama buat directory, didalam directory yang baru saja kita buat, insialisasi npm menggunakan perintan 
```bash
npm init -y
```

setelah melakukan inisilasisasi, kita download package `express` dan `jsonwebtoken`
```bash
npm i express jsonwebtoken
```

setelah proses install selesai, buat file app.js dan tambahkan kode berikut ini
```javascript
const express = require("express")
const app = express()
const jwt = require("jsonwebtoken")

app.use(express.json())

// login route
app.post('/login', (req, res) => {
  const { username, password } = req.body
  if (username === "user1" && password === "1234") {
    jwt.sign({data: username}, 'secret-key', {expiresIn: "1h"}, (err, token) => {
      if (err) res.json({ message: err})
      else res.json({ accessToken: token })
    })
  }else{
    res.json({ message: "username or password incorrect" })
  }  
})

// protected route
app.get('/users', checkToken, (req, res) => {
  res.json({ message: "Hallo users!" })
})

// middleware validasi token
function checkToken(req, res, next) {
  let token = req.headers.authorization
  if(!token) return res.json({ message: "token not found"})
  
  token = token.split(" ")[1]
  jwt.verify(token, 'secret-key', (err, decoded) => {
    if (err) res.json({ message: err})
    else next()
  })
}

app.listen(3000, () => console.log("server running..."))
```

pada route `login`, jika user berhasil login maka akan deberikan JSON web token

kemudian pada route `/users`, kita menambahkan middlware untuk menverifikasi token

jika token valid akan menampilkan pesan `Hallo users!`

selanjutnya jalan server

buka postman, kemudian akses route `/login`, jika login berhasil server akan mengirimkan token
{{<image src="/jwt/part2-login.PNG">}}

selanjutnya akses route `/users` dengan menyertakan token pada header menggunakan format berikut
```json
Authorization = Bearer <Token>
```

Jika token valid akan menampilkan pesan berikut
{{<image src="/jwt/part2-users-check-token.PNG">}}

Jika kita ubah token satu huruf saja maka token tidak valid, berikut hasilnya
{{<image src="/jwt/part2-invalid-token.PNG">}}

Cukup sekian pembahasan konsep JSON web token, jika ada pertanyaan silahkan tuliskan dikolom komentar