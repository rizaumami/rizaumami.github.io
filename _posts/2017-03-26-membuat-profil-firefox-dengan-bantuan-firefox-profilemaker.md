---
layout: post
title: Membuat Profil Firefox dengan Bantuan Firefox Profilemaker
tags: [firefox, profile, security, privacy]
comments: true
---

Peramban situs web kini ibarat sistem operasi tersendiri. Bukan lagi sekadar penampil berkas HTML, melainkan juga berkas multimedia. [Google Chromebooks](https://www.google.com/chromebook/) merupakan bukti nyata bahwa peramban web sanggup menjadi sistem operasi (atau setidaknya menjadi antarmuka grafis) mandiri.

_With great power, come great responsibility_...  
Seiring bertambahnya tugas yang mesti dipikul, maka peramban pun tumbuh menjadi sosok yang rakus sumberdaya komputer, rumit, dan menjadi salah satu celah keamanan.  
Celah keamanan? Yep, peramban web sekarang lumayan menyeramkan, tidak hanya bisa membaca HTML, namun juga bisa merekam video dan suara, menentukan letak, mengetahui kapasitas batere, dan banyak lagi.

Dari banyak peramban web yang kini beredar, jika diusut akan mengerucut pada dua buah peramban besar; yakni Google Chrome dan Mozilla Firefox.  
Sebagai seorang pengguna Firefox, saya sering kali buta dengan fitur-fitur dari Firefox itu sendiri; apa fungsi A? Bagaimana mematikan fitur B? Dan lain sebagainya.  
Artikel kali ini akan membahas bagaimana kita memahami dan menyesuaikan pengaturan Firefox agar sesuai yang kita inginkan.  

Kita bisa saja mengatur fitur-fitur di Firefox satu-per-satu secara manual, namun dengan banyaknya fitur, letaknya yang acak, dan dokumentasi yang minim dalam perambannya itu sendiri, menjadikan langkah mengatur secara manual ini membosankan.

Memperkenalkan, [Firefox Profilmaker](https://ffprofile.com/). Sebuah laman web yang akan membantu kita membuat profil sesuai kehendak. Begini caranya:

1. Kunjungi laman [ffprofile](https://ffprofile.com/) dan lalui tahapannya. Baca dengan seksama setiap parameter dan kegunaannya, hingga kita akan paham apa yang terbaik.
1. Setelah sampai pada tahap _finish_, unduh `profile.zip` hasil pilihan kita pada langkah pertama.
1. Buat sebuah profil baru (atau bisa juga menggunakan yang telah ada)
1. Ketik `about:support` pada _url bar_.
1. Tekan tombol _Open Directory_ pada baris _Profile Directory_, langkah ini akan membuka sebuah jendela _file manager_ pada direktori profil yang terpilih.
1. Tutup Firefox.
1. Hapus semua isi direktori profil di langkah 5.
1. Uraikan `profile.zip` yang telah diunduh di langkah kedua ke dalam direktori profil yang telah dikosongkan isinya tadi.
1. Jalankan kembali Firefox. Jika tadi membuat profil baru, jalankan dengan `firefox -no-remote -P namaprofil`
1. Buka _addon manager_ dan mutakhirkan isinya.

_Easy isn't it?_  
_Thanx for reading, see you on next article..._
