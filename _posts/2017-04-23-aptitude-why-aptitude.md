---
layout: post
title: aptitude why aptitude
tags: [aptitude]
comments: true
---

[`aptitude`](https://aptitude.alioth.debian.org/doc/en/) merupakan _front end_ berdasar [`ncurses`](https://en.wikipedia.org/wiki/Ncurses) untuk [`APT` (_advanced Package Tool_)](https://wiki.debian.org/Apt), penata paket Debian. Meski berembel-embel `ncurses`, `aptitude` tidak mesti dijalankan dalam moda _terminal user interface_, namun bisa hanya dengan _command line interface_.  

Entah sejak kapan saya menggunakan `aptitude`. Namun seingat yang dapat diingat, saya selalu menggunakan `aptitude` dibanding `apt-get` yang lebih umum. Tidak lagi ingat apa yang menjadi landasan teknis kala itu sampai memilih `aptitude`, namun sepertinya lebih ke faktor _user friendlyness_ dan _human readability_ dari `aptitude`.  
Jika dalam menggunakan `apt-get` mesti menghapal beberapa perintah untuk melaksanakan fungsi berbeda walau masih berkenaan dengan penataan paket (misal `apt-get` dan `apt-cache`), `aptitude` cukup hanya menggunakan perintah `aptitude`.

Meski sekarang `apt` (`apt` ditulis dalam huruf kecil, bukan `APT`) juga telah nyaman digunakan, namun saya tetap setia dengan `aptitude`. Selain karena telah menyatu menjadi _muscle memory_, keluaran `aptitude` saya rasa lebih bersih dan mudah dipahami.

Berikut sedikit _cheatsheet_ perintah `aptitude` yang sering saya pakai.

- `aptitude install <nama_paket>`  
  Sudah jelas. Untuk memasang paket, misal `sudo aptitude install yad`.
- `aptitude remove <nama_paket>`  
  Ini juga jelas. Untuk membuang paket, misal `sudo aptitude remove budgie-desktop`. Tapi saya lebih menyukai _purge_ dibanding _remove_ karena dirasa lebih tuntas.
- `aptitude purge <nama_paket>`  
  Membuang paket beserta berkas-berkas pengaturannya (_configuration files_). Jika sebelumnya Anda hanya membuang paket menggunakan _remove_, gunakan `sudo aptitude purge ~c` untuk membuang berkas-berkas konfigurasi yang tersisa.
- `aptitude hold <nama_paket>`  
  Menahan paket agar tidak turut dimutakhirkan. Misal karena diketahui paket versi yang lebih baru memiliki _bug_ yang berpengaruh bagi kita. 
- `aptitude unhold <nama_paket>`  
  Melepaskan _hold_ bagi paket tertentu. Misal jika sebelumnya paket ditahan agar tidak mendapat _update_ karena terdapat _bug_, namun kini _bug_ tersebut telah diperbaiki.
- `aptitude forbid-version <nama_paket>`  
  Cegah `aptitude` agar jangan memutakhirkan paket ke versi tertentu. Misal `aptitude forbid-version vim=1.2.3.rusak-4`.
- `aptitude update`  
  Mengunduh daftar paket termutakhir dari _mirror_ agar dapat mengetahui pembaharuan apa saja yang kini tersedia untuk sistem kita.
- `aptitude safe-upgrade`  
  Melakukan _upgrade_ secara aman. Tidak akan membuang paket yang terpasang kecuali tidak ada paket lain yang membutuhkannya.
- `aptitude full-upgrade`  
  Melakukan peningkatan sistem sepenuhnya. Jika perlu, ia akan memasang paket yang baru dan membuang paket yang lama yang tidak dibutuhkan. Perintah ini lebih cocok untuk sistem yang bergulir (_rolling_) terus seperti Debian Sid.
- `aptitude search <nama_paket>`  
  Mencari paket. Baik yang telah terpasang, atau belum terpasang. Misal untuk mencari paket terpasang yang mengandung kata "gnome"; `aptitude search '~i gnome'`. Atau mencari paket yang nama awalnya adalah "kde"; `aptitude search ^kde`. 
- `aptitude show <nama_paket>`  
  Menampilkan keterangan yang rinci mengenai suatu paket.
- `aptitude versions <nama_paket>`  
  Menampilkan versi dari paket.
- `aptitude clean`  
  Menghapus semua paket yang telah diunduh. Berguna jika kita hendak menjaga sistem tetap langsing.
- `aptitude autoclean`  
  Menghapus versi lama dari paket yang telah diunduh. Berguna jika kita ingin menjaga sistem tetap ramping, namun menginginkan untuk punya berkas `.deb` cadangan.
- `aptitude changelog <nama_paket>`  
  Menampilkan catatan perubahan dari paket. Bisa dijadikan sebagai bahan pertimbangan apakah _upgrade_ paket tersebut memang diperlukan.
- `aptitude download <nama_paket>`  
  Hanya mengunduh paket, tanpa memasangnya.
- `aptitude reinstall`  
  Memasang ulang suatu paket. Merupakan langkah singkat dari `sudo aptitude remove <nama_paket> && sudo aptitude install <nama_paket>`.
- `aptitude why <nama_paket>`  
  Pernah merasa bingung mengapa paket tertentu harus atau terpasang dalam sistem? Gunakan perintah ini untuk mengetahui alasannya. Dari perintah inilah judul artikel ini berasal; `aptitude why aptitude`.  
- `aptitude why-not <nama_paket>`  
  Mengapa paket tertentu tidak bisa dipasang di sistem kita? Cari tahu alasannya menggunakan perintah ini.

Demikian perintah `aptitude` yang sering saya pakai sehari-hari. Masih banyak lagi fungsinya yang jarang atau hampir tidak pernah saya sentuh, _mangga_ rujuk ke manual `aptitude` saja.
