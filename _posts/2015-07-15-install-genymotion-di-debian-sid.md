---
layout: post
title: Install Genymotion di Debian Sid
tag: [android, debian, emulator]
comments: true
---

[Genymotion](https://www.genymotion.com) adalah emulator Android untuk x86. Karena bentuknya yang berupa `VirtualBox` *virtual machine*, Genymotion dapat dijalankan di semua sistem operasi yang terdapat `VirtualBox` di dalamnya. Genymotion bermanfaat bagi Anda yang mengembangkan aplikasi untuk Android, menjalankan aplikasi Android dengan tenaga super dari komputer, atau bahkan sekadar ingin mencoba aplikasi Android karena tidak memiliki *handset*-nya. :sweat_smile:

*So, how to install* Genymotion on Debian Sid? Pastikan Anda telah memasang `VirtualBox`, kemudian ikuti langkah berikut:

* Unduh Genymotion dari situsnya di [https://www.genymotion.com](https://www.genymotion.com)

  Mungkin diperlukan pendaftaran terlebih dahulu. Atau jika enggan mendaftar, bisa dengan mengunjungi [AUR](https://aur.archlinux.org/packages/genymotion/) atau [AUR4](https://aur4.archlinux.org/packages/genymotion/), di kolom *source* Anda akan menemukan *direct link* berkas Genymotion.

  Dicontohkan berkas Genymotion yang diunduh adalah versi 2.5 64bit:

  ```sh
  sudo wget http://files2.genymotion.com/genymotion/genymotion-2.5.0/genymotion-2.5.0_x64.bin -O /opt  
  ```

* Berpindah ke `/opt` dan pasang Genymotion.

  ```sh
  cd /opt
  sudo ./genymotion-2.5.0_x64.bin
  ```

* Setelah proses pemasangan, jangan dahulu menjalankan Genymotion. Genymotion dibekali pustaka `Qt` yang tidak cocok dengan pustaka `Qt` dalam repo Debian, karenanya kita akan pindahkan berkas tersebut agar Genymotion hanya menggunakan pustaka `Qt` dari Debian.

  ```sh
  cd genymotion/
  mkdir Qt-asli
  mv libQt* Qt-asli/
  ```

* Baru kemudian kita pasang pustaka `Qt` pengganti:

  ```sh
  sudo aptitude install libqt4-script libqt4-sql libqt4-svg libqtwebkit4
  ```

* Genymotion ini tidak dibekali berkas `.desktop` sehingga tidak akan muncul di menu *desktop environment*. Karenanya, baik kita buat berkas *launcher* tersebut.

  ```sh
  sudo nano /usr/share/applications/genymotion.desktop
  ```

  Dan isikan *string* berikut:

  ```sh
  [Desktop Entry]
  Encoding=UTF-8
  Version=1.0
  Type=Application
  Name=Genymotion
  Comment=Virtual environment for Android
  Exec=/opt/genymotion/genymotion
  Icon=/opt/genymotion/icons/icon.png
  Terminal=false
  Categories=Emulator;System;Development;Application;
  ```

*That's all folks*. :kissing_heart:
