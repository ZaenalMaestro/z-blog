---
title: "Berkenalan Dengan Nodejs"
date: 2021-02-05T10:31:10+08:00
draft: false
thumbnail: "nodemin.png"
---
### 1 Instalasi hugo

### 2 cara membuat website baru
command : `hugo new site <nama-site>`
### 2 cara membuat website tema
command : `hugo new theme <nama-theme>`
- membuat layout
  
struktur folder layouts

![struktur folder layouts](/img/nodejs/struktur-folder-layout.PNG)
#### folder `_default`
folder default merupakan folder default dari hugo yang berisi file
- `baseof.html` merupakan file yang akan meload semua file didalam folder layouts atau biasa disebut sebagai tempalate, berikut scriptnya
```html
<!DOCTYPE html>
<html>
    {{- partial "head.html" . -}}
    <body>
        {{- partial "header.html" . -}}
        <div id="content">
        {{- block "main" . }}{{- end }}
        </div>
        {{- partial "footer.html" . -}}
    </body>
</html>
```
penjelasan kode
- *`{{- partial "<nama_file>" . -}}` berfungsi untuk me-load file difolder `partial`
- `*{{- block "main . }}{{- end}}` untuk memuat file utama seperti halaman index.html

- `list.html` merupakan file yang akan meload daftar konten yang spesifik contohnya menampilakan semua artikle seri "belajar nodejs" berikut contohnya
```html
{{define "main"}}
<h1>{{.Title}}</h1>
<ul>
   {{range .Pages}}
   <li>
      <a href="{{.Permalink}}">{{title}}</a>
   </li>
   {{end}}
</li>
{{end}}
```
penjelasan kode 
contohnya kita mengakses halaman localhost:1313/blog/
- `{{define "main"}}` artinya semua script list file `blog` akan berada didalam `block main` yang berada di file `baseof.html`
- {{range .Pages}} artinaya hanya meload semua file dari folder yg spesifik conhtonya hanya menampilkan semua file difolder blog
- `{{.Permalink}}` dan `{{{.Title}}}`, untuk permalink akan menampilan url ke file yg spesfik sedangkan title setelah permalink akan menampilkan `title archetype` dari `<file>.md` yang spesifik


- `single.htmt` file yang menangani file yang spesifik berikut scriptnya
- 
```html
{{define "main"}}
<h1>{{.Title}}</h1>
<span>Dibuat pada{{.Date.Format "2021-2-4"}}</span>
<section>
   <p>{{.Content}}</p>
</section>
{{end}}
```
`{{.Content}}` berfungsi untuk meload file markdown 

### #partial 
merupakan folder untuk menyimpan fitur" website kita seperti header, footer, dan sidebar

### index.html
merupakan file landing page website kita

### 404.html
file yang menampilkan halaman yang tidak ditemukan


