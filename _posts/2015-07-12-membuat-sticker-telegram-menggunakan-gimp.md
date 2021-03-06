---
layout: post
title: Membuat Sticker Telegram Menggunakan GIMP
tag: [telegram, gimp, stiker]
comments: true
---

Kadang ada hasrat yang sulit digambarkan dengan kata-kata ketika kita sedang ngobrol di Telegram, di sinilah gunanya *sticker* sebagai penyambung pikiran. Beda dengan aplikasi *chat* lain yang mengkomersilkan stiker, Telegram menggratiskan stiker dan bahkan memungkinkan pengguna untuk membuat stiker sendiri.

*Sticker is usefull*, kemungkinan membuat stiker mandiri membuatnya luar biasa. *With great power, come great responsibility*, kemungkinan membuat stiker sendiri banyak disalah-gunakan pengguna Telegram dengan mengambil stiker dari aplikasi *chat* lain, yang umumnya stiker tersebut berbayar, dan merubahnya jadi stiker Telegram.

Artikel ini tidak akan mengajarkan hal tersebut, di sini kita akan membuat stiker dari sumber gambar yang tidak melanggar hak cipta. Gambar di sini adalah gambar dengan banyak gradasi warna dan *background*. Untuk gambar datar semacam logo atau gambar kartun, bisa langsung dirubah menggunakan `cwebp`.

*Let's start!*

* Stiker telegram menggunakan format gambar [webp](https://en.wikipedia.org/wiki/Webp), karenanya kita pasang dahulu paket `cwebp` menggunakan perintah `sudo aptitude install cwebp`.

* Buka gambar yang hendak dijadikan stiker menggunakan `GIMP`, usahakan resolusinya tidak kurang dari 512*512px.

* Tekan bersamaan `SHIFT`+`CTRL`+`D` untuk menyalin *layer* agar jika terjadi kesalahan kita masih memiliki gambar sumbernya.

* Sembunyikan *layer* bawah agar tidak mengganggu pekerjaan.
Pilih *layer* dasar dengan menekan kunci `END` atau klik *layer* pada *layer dialog*, kemudian klik pada kolom *visibility* (berupa ikon mata). Jika ikon mata hilang berarti *layer* telah disembunyikan.

* Pilih kembali *layer* atas dengan menekan kunci `HOME` atau dengan klik *layer* pada *layer dialog*.

* Buat *transparency* pada gambar dengan cara `klik kanan - Layer - Transparency - Add Alpha Channel`

* Tekan kunci `U` untuk menggunakan `Fuzzy Select Tool`.

* Gunakan `Fuzzy Select Tool` untuk menghapus area yang tidak diinginkan. Untuk memperluas area cakupan `Fuzzy Select Tool`, bisa dengan sambil menekan kunci `SHIFT` atau setelah menekan *mouse* dilanjutkan menggeser `mouse` ke kiri-kanan.

  [![Antarmuka GIMP](http://i.imgur.com/g0b4jrw.png)](http://i.imgur.com/g0b4jrw.png)

* Jika dirasa telah cukup. Saatnya untuk memberi batas putih di pinggiran gambar.
Pilih area (*transparent*) di luar gambar menggunakan `Fuzzy Select Tool`, jangan lupa centang *Feather edges* pada `Fuzzy Select Tool` *option*. Kemudian balik (*invert*) pilihan menggunakan `CTRL+I`.

  Tukar *foreground* dan *background* hingga *foreground* menjadi warna putih.
  Beri garis putih setebal 6px pada pinggiran gambar menggunakan `Edit - Stroke Selection..`.

* Hapus *layer* yang tidak terpakai

* *resize* dimensi gambar menjadi lebar atau tinggi maksimal 512px dan resolusi 300 ppi.

* Simpan sebagai berkas PNG.

* Rubah berkas PNG ke webp menggunakan perintah:

  ```sh
  cwebp -lossless <gambar_masukan> -o <gambar_stiker>.webp
  ```
  
  Atau jika Anda menggunakan Gnome, bisa membuat `nautilus` *script*;

  ```sh
  #!/bin/bash

  while [ $# -gt 0 ]; do
    picture=$1
    cwebp -lossless "$picture" -o "${picture%.*}.webp"
    shift
  done
  ```

Simpan di `$HOME/.local/share/nautilus/scripts`. Tiap kali hendak merubah gambar menjadi stiker, tinggal klik `kanan gambar - Scripts` dan pilih *script*.

