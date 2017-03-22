---
layout: post
title: Compile SimpleScreenRecorder di Debian Sid
tag: [compile, screencast, recorder, debian]
comments: true
---

`SimpleScreenRecorder` adalah perekam layar bagi Linux. `SimpleScreenRecorder` nampaknya adalah salah satu aplikasi perekam layar yang terbaik di Linux, yang sayangnya tidak ada dalam repo resmi Debian.

Artikel ini akan menunjukkan bagaimana membuat paket Debian dari `SimpleScreenRecorder`. Jika Anda hanya perlu memasang `SimpleScreenRecorder` dan tidak memerlukan paket Debian-nya, cukup ikuti langkah yang dijabarkan di laman [https://github.com/MaartenBaert/ssr](https://github.com/MaartenBaert/ssr).

* Salin repo utama `SimpleScreenRecorder`

  ```sh
  git clone https://github.com/MaartenBaert/ssr
  ```

* Salin repo paket `SimpleScreenRecorder`

  ```sh
  git clone https://github.com/MaartenBaert/ssr-packages
  ```

  Salin map Debian dari repo paket `SimpleScreenRecorder`. Repo tersebut memang tidak memasukkan distribusi Debian, hanya Ubuntu. Dan karena Ubuntu berdasar Debian, kita dapat menggunakan pengaturan Ubuntu di Debian, tentunya dengan perubahan seperlunya.

  Salin map Debian dari map `package-ubuntu` rilis terkini. Dicontohkan Ubuntu Wily.

  ```sh
  cp -vr ssr-packages/package-ubuntu/wily/simplescreenrecorder-0.3.3/debian/ ssr
  ```

* Masuk ke dalam map `ssr` untuk memulai kompilasi

  ```sh
  cd ssr
  ```

* Sunting berkas `debian/changelog` dari semula:

  ```sh
  simplescreenrecorder (0.3.3+1~ppa1~wily1) wily; urgency=low

    * New release

   -- Maarten Baert (v2) <maarten-baert@hotmail.com>  Mon, 27 Jul 2015 13:43:32 +0200
  ```

  Menjadi:

  ```sh
  simplescreenrecorder (0.3.3) unstable; urgency=low

    * New release

   -- Maarten Baert (v2) <maarten-baert@hotmail.com>  Mon, 27 Jul 2015 13:43:32 +0200
  ```

* Mulai kompilasi

  ```sh
  dpkg-buildpackage -uc -us -rfakeroot
  ```

  Jika kompilasi tehenti karena galat:

  ```sh
  dpkg-checkbuilddeps: error: Unmet build dependencies: help2man libgl1-mesa-dev libglu1-mesa-dev qt4-qmake libqt4-dev libavformat-dev libavcodec-dev libavutil-dev libswscale-dev libasound2-dev libpulse-dev libjack-dev libx11-dev libxext-dev libxfixes-dev libxi-dev  
  dpkg-buildpackage: warning: build dependencies/conflicts unsatisfied; aborting
  dpkg-buildpackage: warning: (Use -d flag to override.)
  ```

  Pasang paket-paket yang dibutuhkan untuk kompilasi:

  ```sh
  sudo apt install help2man libgl1-mesa-dev libglu1-mesa-dev qt4-qmake libqt4-dev libavformat-dev libavcodec-dev libavutil-dev libswscale-dev libasound2-dev libpulse-dev libjack-dev   libx11-dev libxext-dev libxfixes-dev libxi-dev
  ```
  
  Ulangi kompilasi, mestinya kali ini berjalan lancar dan menghasilkan berkas `simplescreenrecorder_0.3.3_amd64.deb` dan `simplescreenrecorder-lib_0.3.3_amd64.deb`.
  
