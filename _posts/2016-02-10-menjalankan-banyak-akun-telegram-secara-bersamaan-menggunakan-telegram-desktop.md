---
layout: post
title: Menjalankan Banyak Akun Telegram Secara Bersamaan Menggunakan Telegram Desktop di Linux
tags: [telegram]
comments: true
---

Ada kalanya kita merasa perlu untuk menjalankan banyak akun Telegram secara bersamaan. Entah untuk menguji `bot`, untuk memisahkan akun menurut keperluan ataukah hal lainnya.
Dengan beragamnya _client_ Telegram, beragam pula cara untuk bertelegram dengan banyak akun secara bersamaan. Paling mudah adalah dengan menggunakan [telegram-cli](https://github.com/vysheng/tg) karena ia memiliki parameter `profile` dan `config`, sedikit merepotkan jika menggunakan [webogram](https://web.telegram.org) yang mesti dibantu _add-ons_ bagi peramban yang dipakai agar bisa menjalankan banyak profil.
Hingga artikel ini ditulis, saya tidak memiliki pengetahuan bagaimana menjalankan banyak akun Telegram secara bersamaan di _mobile client_ (Android, WP, iOS).
Artikel ini akan menerangkan bagaimana menjalankan banyak akun Telegram secara bersamaan dengan menggunakan [Telegram Desktop](https://desktop.telegram.org/) di sistem operasi GNU/Linux.

Telegram Desktop adalah _client_ Telegram dengan antar muka grafis. Ia dibangun menggunakan Qt dan dirancang dapat dijalankan di banyak sistem operasi.
Telegram Desktop juga merupakan salah satu _first class client_ bagi Telegram; didukung resmi dan mendapat pembaharuan secara rutin.

Berikut langkah untuk menjalankan banyak akun Telegram secara bersamaan dengan menggunakan Telegram Desktop:

- Siapkan sebuah direktori baru sebagai _working directory_ bagi akun Telegram. Bisa dengan membuat direktori kosong atau menyalin direktori `~/.TelegramDesktop` dari akun yang telah ada.
Jika menggunakan direktori kosong, maka kita perlu untuk mendaftarkan ulang nomor telpon akun kita. Nama direktori bebas, dan untuk tutor ini kita andaikan namanya adalah `anon` yang terletak di `$HOME`.
- Unduh berkas biner Telegram Desktop terbaru, kemudian uraikan di tempat yang mudah diingat, misal di direktori `~/anon` tadi.
- Jalankan Telegram Desktop dengan akun baru ini menggunakan perintah:

  ```sh
  ~/anon/Telegram -workdir ~/anon
  ```

- Profit!

Langkah tersebut bisa diulangi sebanyak akun yang hendak dijalankan.

Kelemahan langkah di atas adalah kita mesti menyediakan berkas biner Telegram Desktop untuk tiap akun. Jika menginginkan untuk hanya menggunakan sebuah berkas biner Telegram Desktop untuk semua akun kita, _here's how_:

1. Misalkan berkas biner Telegram kita tempatkan di `/opt`
2. Jalankan setiap akun dengan menggunakan perintah berikut:

   ```sh
   /opt/Telegram -many -workdir ~/anon
   ```


