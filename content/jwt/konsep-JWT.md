---
title: "PART 1 - Konsep JSON Web Token"
date: 2021-02-26T20:45:52+08:00
draft: false
author: "Zaenal"
categories: 
- JWT
tags:
- jwt
- json
---

JSON Web Token (JWT) merupakan standar keamanan transmisi informasi menggunakan format `JSON`

dengan menggunakan JSON Web Token, informasi yang dikirimkan dapat diverifikasi dan dipercaya keasliannya
karena ditandangani secara digital 

### Penggunaan JSON Web Token
secara umum JSON Web Token digunakan untuk 
- Autorisasi
- pertukaran informasi

### Struktur JSON Web Token
JSON Web Token memiliki tiga bagian yaitu

#### `header`
Berisi informasi algoritma dan jenis token yang digunakan

header JSON Web Token di encoded menggunakan `Bas64Url`

berikut adalah informasi yang terkandung didalam header
```JSON
{
  "alg": "HS256",
  "typ": "JWT"
}
```

#### `Payload`
Berisi data yang mewakili user contohnya id user atau email

tetapi jangan memasukkan data sensitif pada payload seperti password

payload JSON Web Token juga di `encoded` menggunakan `Bas64Url`

berikut contoh informasi yang bisa dimasukkan kedalam payload
```JSON
{
  "id": "1",
  "name": "Jhon Doe",
  "role": "admin"
}
```

#### `tanda tangan digital (signature)`
tanda tangan digital JSON web token adalah hasil enkripsi dari header dan payload yang di encoded

tanda tangan digital JSON web token mendukung algoritma sepeti HMAC, RSA, dan ECDSA

secara default algoritma yang digunakan yaitu HMAC

berikut contoh pembuatan tanda tangan digital
```javascript
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  kunci-enkripsi)
```

### contoh JSON web token
setiap bagian JSON web token dipisahkan dengan `(.)`

berikut struktur JSON web token
```
header.payload.signature
```

dan berikut contoh token yang telah dibuat
```json
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjoidXNlcjEiLCJpYXQiOjE2MTQzMzAyNjUsImV4cCI6MTYxNDMzMzg2NX0.LHi_r5EQxCkKB9fL8SNQsMrURxC1r6C0LEb0P6wYHIU
```

### cara kerja JSON web token

{{<image src="/jwt/konsep-diagram.PNG">}}

Saat user berhasil login, server akan memberikan JSON web token

jadi saat user ingin mengakes route API yang dilindungi, user harus mengirimkan token saat melakukan request yang disematkan pada http header

berikut contohnya
```json
authorization : Bearer <token>
```

kemudian server akan menverifikasi token yang dikirim 

jika token valid, user diizinkan mengakses route API untuk mendapat informasi dari server

Cukup sekian pembahasan konsep JSON web token, jika ada pertanyaan silahkan tuliskan dikolom komentar

##### Artikel selanjutnya

--->> [Part 2 - Implementasi JSON Web Token Menggunakan Node Js](/jwt/implementasi-jwt-menggunakan-nodejs/)


##### Sumber Referensi:
--->> [Dokumentasi resmi JSON web token](https://jwt.io/introduction)



