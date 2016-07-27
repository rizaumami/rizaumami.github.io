---
layout: post
title: Menjaga Grup Telegram dengan Bantuan Merbot
tags: [telegram,pegelinux,merbot,grup]
comments: true
---

> **EDIT** 2016-04-02: _Telegram kini memiliki SuperGroup yang mampu menampung hingga 5000 anggota. Terdapat banyak fitur tambahan untuk SuperGroup yang membuatnya mampu dijaga kenyamanannya tanpa bantuan bot khusus._

[Telegram](https://telegram.org) adalah aplikasi perpesanan yang mengutamakan kecepatan dan keamanan. Berkat beragam keunggulannya dibanding aplikasi perpesanan serupa, Telegram semakin banyak digunakan.

Sekitar Januari-Februari 2015 saya menggulirkan inisiatif membuat grup Telegram bagi penggemar Linux (bernama [PegeLinux](https://telegram.me/joinchat/AfB26wDqyq6R_8F1spYyNA)) di [sub-forum Linux Kaskus](http://m.kaskus.co.id/thread/54cba5fea4cb170e2c8b456d/pegelinux-telegram-users-group-for-those-who-pegel-with-linux/). Seiring waktu, anggota bertambah, demikian pula permasalahan dalam menjaga agar grup tetap kondusif.

Permasalahan utama dalam menjaga kondisi group adalah *spammer/flooder*. Ancaman tersebut lebih nyata dalam Telegram karena;

- anggota bisa mengundang anggota lainnya,
* anggota bisa merubah nama, _username_, foto profil, bahkan mengosongkan _username_, 
- anggota bisa merubah identitas grup, seperti nama dan foto,
- anggota grup Telegram bisa mencapai 200 orang,
- Telegram memiliki fitur bot, yang jika digunakan oleh `spammer/flooder` bisa sangat merusak dan hampir tak terhentikan.

Untungnya, banyak juga pengguna Telegram yang menyadari ancaman di atas. Telah ada *bot* yang dirancang untuk keperluan penataan grup Telegram, misal [@liberbot](https://telegram.me/liberbot) dan [@uzzbot](https://github.com/uziins/uzzbot).  
Sayangnya, *bot-bot* tersebut tidak bisa mengamankan grup yang telah berdiri, melainkan hanya bisa membuat grup baru dan menjadikan kita sebagai moderator. Karenanya, bisa dikatakan kita hanya menumpang dalam grup tersebut karena kendali sepenuhnya di pihak (pemilik) *bot*.

Akhirnya diciptakanlah **[`merbot`](https://github.com/rizaumami/merbot)**. Sebuah Telegram bot berdasar [`telegram-bot`](https://github.com/yagop/telegram-bot) dan inspirasi dari [`@uzzbot`](https://github.com/uziins/uzzbot). `Merbot` dikhususkan untuk menjaga keamanan dan kenyamanan dalam sebuah grup Telegram, dalam hal ini khususnya PegeLinux.  
Karena sifatnya sebagai penjaga grup, `merbot` sangat disarankan untuk dipasang di *virtual private sever* agar selalu siaga 24 jam.

Silahkan rujuk [repo github untuk merbot](https://github.com/rizaumami/merbot) dan [laman wiki](https://github.com/rizaumami/merbot/wiki)-nya untuk mendalami penggunaan `merbot` sebagai *guardian bot* grup Telegram Anda.
