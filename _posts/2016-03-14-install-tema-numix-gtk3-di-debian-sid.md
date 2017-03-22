---
layout: post
title: Install tema Numix gtk3 di Debian Sid
tags: [numix, theme, gtk, debian]
comments: true
---

[![Numix theme](http://i.imgur.com/v3MpVdZ.jpg)](http://i.imgur.com/v3MpVdZ.jpg)

Tidak diragukan lagi, [Numix](https://github.com/shimmerproject/Numix) merupakan _flat theme_ paling populer untuk mempercantik tampilan desktop GNU/Linux.
_Quality speaks it self_, Numix memang cantik. Dan [Numix Project](http://numixproject.org/) sebagai pembuat Numix juga memiliki solusi lengkap untuk memperindah tampilan desktop, mulai dari tema, _wallpaper_ sampai ikon.

Beberapa produk Numix Project adalah berbayar atau setidaknya menyarankan donasi, jadi jika Anda memiliki sedikit rezeki berlebih, tentunya lebih baik jika menyumbang atau membeli produk mereka sebagai bentuk dukungan kita.
Dan bagi mereka yang belum sanggup membeli atau menyumbang, entah karena alasan ekonomi atau karena kesulitan dalam pembayaran, tema Numix gtk3 ini dapat diperoleh dengan gratis.

Jadi, sekarang mari kita percantik tampilan Debian kita ...

- _Clone_ repo Numix dari github

  ```sh
  git clone https://github.com/shimmerproject/Numix
  ```

- Numix menggunakan [Sass](http://sass-lang.com/) sebagai _compiler_, karenanya install Sass terlebih dahulu

  ```sh
  sudo apt install ruby-sass
  ```

- Pasang juga _dependencies_ yang diperlukan untuk membuat biner `gresource`:

  ```sh
  sudo apt install libxml2-utils libgdk-pixbuf2.0-dev
  ```

- Mulai kompilasi Numix

  ```sh
  make
  sudo make install
  ```


Tema Numix siap digunakan.
Ada dua cara untuk menetapkan tema Numix dalam Gnome 3;
1. Menggunakan GUI dari paket `gnome-tweak-tool`
1. Menggunakan perintah `gsettings` sebagai berikut:  

   ```sh  
   gsettings set org.gnome.desktop.interface gtk-theme "Numix"  
   gsettings set org.gnome.desktop.wm.preferences theme "Numix"  
   ```

Untuk DE lain, silahkan rujuk laman github Numix di: [https://github.com/shimmerproject/Numix](https://github.com/shimmerproject/Numix).





