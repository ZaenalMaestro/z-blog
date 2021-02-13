---
title: "3. Express - Route Modular"
date: 2021-02-12T10:44:24+08:00
draft: false
author: "Zaenal"
categories: 
- express js
tags:
- expressjs
- javascript
- nodejs
---

Route modular sederhananya kita membagi program dalam aplikasi kita menjadi
bagian-bagian yang lebih spesifik berdasarkan fungsinya masing-masing. 

untuk membuat route modular, kita menggunakan class `express.Router` yang telah disediakan oleh framework express yang akan kita tambahkan disetiap route

langsung saja kita mulai, pertama buat file `index.js`, kemudian buat directory `routes`, didalam directorynya buat file `mahasiswaRoute.js` dan `dosenRoute.js`
{{<image src="/expressjs/route-modular1.PNG" alt="route modular">}}

didalam file `mahasiswaRoute.js` tambahkan script berikut
```javascript
const express = require('express'),
   router = express.Router()

router.get('/show', (req, res) => {
   res.send('Menampilkan data dosen')
})
router.post('/insert', (req, res) => {
   res.send('Menambahkan data dosen')
})
router.put('/update', (req, res) => {
   res.send('Mengubah data dosen')
})
router.delete('/delete', (req, res) => {
   res.send('Menghapus data dosen')
})

module.exports = router
```

didalam file `dosenRoute.js` tambahkan script berikut
```javascript
const express = require('express'),
   router = express.Router()

router.get('/show', (req, res) => {
   res.send('Menampilkan data mahasiswa')
})
router.post('/insert', (req, res) => {
   res.send('Menambahkan data mahasiswa')
})
router.put('/update', (req, res) => {
   res.send('Mengubah data mahasiswa')
})
router.delete('/delete', (req, res) => {
   res.send('Menghapus data mahasiswa')
})

module.exports = router
```

kemudian didalam file `index.js` tambahkan script berikut
```javascript
const express = require('express'),
   app = express()

// import router
const mahasiswaRoute = require('./routes/mahasiswaRoute')
const dosenRoute = require('./routes/dosenRoute')

app.use('/mahasiswa', mahasiswaRoute)
app.use('/dosen', dosenRoute)

app.listen(3000)
console.log('Server running on http://localhost:3000')
```
perhatikan pada potongan script berikut untuk menggunakan router yang telah kita buat
```javascript
app.use('/mahasiswa', mahasiswaRoute)
app.use('/dosen', dosenRoute)
```
didalam `app.use` kita memberi `route path` yang nantinya digabung dengan `route path `masing-masing router, contohnya seperti berikut
```javascript
// routes/mahasiswaRoute.js
router.get('/show', (req, res) => {
   res.send('Menampilkan data mahasiswa')
})
```
```javascript
// index.js
app.use('/mahasiswa', mahasiswaRoute)
```
jika kita ingin menampilkan data mahasiswa di route `mahasiswaRoute` kita menggunakan url `http://localhost:3000/mahaiswa/show`

untuk membuatnya lebih modular, kita bisa membuat file `routes.js` yang akan mengimport semua route yang telah dibuat
{{<image src="/expressjs/route-modular.PNG" alt="route modular">}}

tambahkan script berikut didalam file `routes.js`
```javascript
const express = require('express'),
router = express.Router()

router.use('/mahasiswa', require('./routes/mahasiswaRoute'))
router.use('/dosen', require('./routes/dosenRoute'))

module.exports = router
```

kemudian ubah file `index.js` menjadi seperti ini
```javascript
const express = require('express'),
   app = express()

// import router
const routes = require('./routes.js')
app.use()

app.listen(3000)
console.log('Server running on http://localhost:3000')
```

sekarang route express kita sudah semakin modular

cukup sekian pembahasan tentang route modular dan semoga bermanfaat. 

--->> Materi Selanjutnya [4. express - Midleware](/expressjs/midleware/)




