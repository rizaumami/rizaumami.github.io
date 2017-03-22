---
layout: post
title: Install Windows Font dalam Debian
tags: [windows, debian, linux, font]
comments: true
---

Mengapa memasang huruf Microsoft Windows dalam Linux? Windows adalah sistem operasi yang paling banyak digunakan di dunia, karenanya besar kemungkinan dokumen yang kita dapatkan dari rekan adalah dibuat dalam sistem operasi Windows.
Jika dokumen tersebut dibuka dalam Linux, maka kemungkinan besar tampilannya akan tidak sesuai yang dimaksudkan si pembuat dokumen, hal ini karena jenis huruf mempengaruhi tata letak dan tampilan dokumen.

Telah ada upaya mengurangi permasalahan ini, misal dengan menggunakan huruf serba-cocok (*compatible*) dari [Liberation Fonts](https://en.wikipedia.org/wiki/Liberation_fonts) atau [*Chrome OS core fonts*](https://en.wikipedia.org/wiki/Croscore_fonts) dari Google, bahkan [*Core Fonts*](https://en.wikipedia.org/wiki/Core_fonts_for_the_Web) dari Microsoft.

Gunakan perintah berikut untuk memasang;

- Liberation  

  ```sh
  sudo aptitude install fonts-liberation
  ```

- Croscore  

  ```sh
  sudo aptitude install fonts-croscore
  ```

- Microsoft Core  

  ```sh
  sudo aptitude install ttf-mscorefonts-installer
  ```
  
  Sayangnya, versi huruf dari [*Core Fonts*](https://en.wikipedia.org/wiki/Core_fonts_for_the_Web) telah tertinggal jauh dari huruf yang sama yang berada dalam sistem Windows teranyar.  
  Artikel ini akan menjelaskan langkah bagaimana mendapatkan huruf Microsoft Windows 10 dari berkas *installer*-nya. Patut diingat, [*Core Fonts*](https://en.wikipedia.org/wiki/Core_fonts_for_the_Web) dilisensikan untuk bebas digunakan, sementara dalam artikel ini kita akan mengambil langsung dari berkas *installer* bahkan dengan tanpa memasang sistem operasi Windows. Menurut hemat saya, selama kita memiliki lisensi Windows 10, baik dari membeli baru atau hasil *upgrade*, maka kita berhak atas kandungan Windows tersebut. *But, I'm not a lawyer*...

* Unduh berkas ISO Microsoft Windows 10 di [http://www.microsoft.com/en-us/software-download/windows10ISO](http://www.microsoft.com/en-us/software-download/windows10ISO).

* Pasang `wimlib`. `wimlib` sudah ada dalam repo Sid. Namun jika Anda menggunakan Debian Jessie, silahkan rujuk artikel [Compile wimlib di Debian Sid](http://rizaumami.github.io/2015/07/09/compile-wimlib-di-debian-sid/).  

  ```sh
  sudo aptitude install wimlib
  ```

* Kaitkan berkas ISO.  

  ```sh
  sudo mount Win10_English_x64.iso /mnt/iso
  ```

* Buat map khusus huruf Microsoft di `/usr/share/fonts/truetype`  

  ```sh
  sudo install -dm755 /usr/share/fonts/truetype/windows10
  ```

* Ambil huruf hanya *truetype* ISO dan simpan di `/usr/share/fonts/truetype/windows10`  

  ```sh
  sudo wimextract /mnt/iso/sources/install.wim 1 /Windows/Fonts/"*".ttf --dest-dir /usr/share/fonts/truetype/windows10  
  ```

* Mutakhirkan *font cache*  

  ```sh
  sudo fc-cache -s
  ```

* Lepaskan berkas ISO dari kaitan.  

  ```sh
  sudo umount /mnt/iso
  ```

*Done!*.
