---
layout: post
title: Kiat Menjaga Debian Sid Tetap Ramping dan Sehat
tags: [debian, deborphan, localepurge]
comments: true
---

Setelah sekian lama _distro hopping_, akhirnya saya memilih menjejakkan kaki di Debian Sid. Debian Sid menyediakan apa yang saya harapkan dari sebuah distro; _big user and  developer base, security, flexibility, policy, and update speed_.

Seperti halnya manusia yang tidak menjaga asupan makanannya, maka dia rentan menjadi gendut dan sakit. Demikian juga halnya dengan sistem operasi, jika asal pasang piranti lunak akan rentan terkena _bug_ atau masalah lain yang menyebabkannya tidak bekerja dengan sempurna.

Artikel kali ini akan mengupas bagaimana upaya yang dapat dilakukan untuk menjaga Debian Sid tetap ramping dan bekerja optimal.

* ### Hindari memasang paket yang kurang atau tidak diperlukan

  Diet atau asupan makanan adalah hal utama yang mempengaruhi kesehatan manusia. Ini berlaku juga pada sistem operasi. Semakin banyak paket berarti semakin banyak faktor yang bisa menjadi penyebab galat.
  
  Ketika kita memasang paket, APT akan turut memasang paket yang _recommended_ dengan tujuan agar paket yang dipasang dapat bekerja optimal. Hal ini sangat membantu bagi pengguna yang masih awam atau memang tidak begitu paham dengan kebutuhan paket yang dipasangnya.  
  
  Jika kita yakin bahwa kita tidak memerlukan paket yang disarankan (_recommended_) tersebut, kita dapat meminta APT agar tidak memasangnya. 

  ```bash
  sudo printf "%s\n" 'APT "";' 'APT::Install-Recommends "false";' > /etc/apt/apt.conf.d/10recommends  
  ```
  
* ### Waspada terhadap paket yang bermasalah

  Aliran paket dalam Debian adalah sebagai berikut; paket baru dari hulu (_upstream_) mengalir ke `Experimental`, kemudian ke `Unstable`, lalu ke `Testing`, dan akhirnya menjadi Debian `Stable`.
  
  Debian Sid adalah nama sandi bagi Debian Unstable. Seperti namanya, Debian ini tidak stabil, karena merupakan salah tahap pengujian bagi paket Debian yang akhirnya akan berakhir di Debian Stable.  
  Meski paket telah menjalani tahap `Experimental` terlebih dahulu, seringkali paket yang turun ke `Unstable` masih _buggy_. Ini wajar dan memang diharapkan, karena lebih baik _bug_ tertangkap di `Unstable` atau `Testing` daripada lolos ke `Stable`. 
  
  Nah, bagaimana agar kita waspada (_aware_) terhadap paket berkutu dari `Experimental`?  
  Adalah dengan memasang paket `apt-listbugs`.

  ```bash
  sudo apt install apt-listbugs
  ```

  Lalu, apa yang harus dilakukan jika ternyata ketika memutakhirkan sistem, `apt-listbugs` memberitahu bahwa terdapat paket bermasalah?  Mari lihat kiat berikutnya.
  
* ### Jangan mutakhirkan paket bermasalah

  Jika ternyata ketika memutakhirkan sistem kita mendapati peringatan bahwa terdapat paket yang bermasalah, tahan _update_ untuk paket tersebut!  
  Gunakan `apt-mark` untuk menahan paket tersebut sampai tersedia perbaikannya.
  
  ```bash
  sudo apt-mark hold nama-paket
  ```
  
  Jika _bug_ telah diperbaiki, _unhold_ paket tersebut agar dapat kembali menerima pembaharuan.
  
  ```bash
  sudo apt-mark unhold nama-paket
  ```
  
* ### Selalu gunakan full-upgrade

  `apt` dan `apt-get` memiliki dua cara untuk memutakhirkan sistem, yakni; (1) `upgrade`, dan (2) `full-upgrade`. Demikian juga dengan `aptitude` yang memiliki `safe-upgrade` dan `full-upgrade`.
  
  Debian Sid lebih tepat menggunakan `full-upgrade` karena ia akan memasang paket ke versi paling anyar, membuang paket yang tidak lagi diperlukan, juga akan memasang paket yang dibutuhkan dan paket baru yang sebelumnya tidak ada.
  
  ```bash
  sudo apt full-upgrade
  ```
  
* ### Buang paket yang tidak dibutuhkan

  Bisa dengan cara manual, atau dengan bantuan `deborphan`.
  
  ```bash
  sudo aptitude install deborphan
  sudo deborphan
  ```
  
  Periksa keluaran `deborphan`, jangan main buang paket-paket yang katanya _orphaned_.
  Gunakan `aptitude why nama-paket` untuk mengetahui mengapa paket tersebut bisa berada dalam sistem, dan gunakan `aptitude why-not nama-paket` untuk mengetahui amankah paket tersebut untuk dibuang.

* ### Buang paket secara tuntas

  Gunakan `sudo aptitude purge nama-paket` untuk membuang paket beserta semua berkas pengaturannya (_configuration files_).  
  Jika sebelumnya Anda hanya menggunakan `sudo aptitude remove nama-paket` dan sekarang ingin membuang berkas-berkas konfigurasi paket yang masih tersisa, gunakan perintah `sudo aptitude purge ~c`.

* ### Kosongkan tembolok (_cache folder_)

  Ketika kita melakukan pembaharuan pada sistem, maka sistem akan mengunduh paket-paket yang lebih anyar dan menyimpannya di `/var/cache/apt/archives`.  
  Seiring Debian Sid yang kerap mendapat pembaharuan, maka isi `/var/cache/apt/archives` akan semakin banyak dan ukurannya membengkak. 
  
  Jika Anda tidak akan mencadangkan paket-paket tersebut dan akan membuangnya, gunakan perintah berikut:
  
  - Jika hanya ingin membuang paket versi lama
  
    ```bash
    sudo aptitude autoclean
    ```
  
  - Jika ingin menghapus SEMUA paket dari _cache_
  
    ```bash
    sudo aptitude clean
    ```

* ### Hapus berkas terjemah (_locale files_)

  Banyak paket yang mengandung berkas yang tidak terlalu berguna bagi saya, misalnya berkas terjemah atau dokumentasi dalam bahasa asing yang tidak dimengerti.  
  Bisakah kita membuang berkas-berkas tersebut?
  
  Jawabannya adalah Ya! Paket `localepurge` akan membuang semua berkas _locales_ kecuali yang tertera dalam `/etc/locale.nopurge`.  
  Tergantung dari paket yang Anda pasang dalam sistem, `localepurge` bisa menghemat ratusan _megabyte_ ruang penyimpanan.
  
  MAKLUMAT: `localpurge` adalah _hack_ yang tidak terpadu dengan manajemen paket Debian, yang mungkin akan menimbulkan hal-hal yang tidak diinginkan. Misal, jika Anda juga menggunakan `debdelta`, maka `localepurge` akan menghapus berkas-berkas yang dibutuhkan `debdelta` untuk membuat delta atau _rebuild_ paket.
