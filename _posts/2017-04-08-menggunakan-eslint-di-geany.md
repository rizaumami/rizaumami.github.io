---
layout: post
title: Menggunakan ESLint di Geany
tags: [geany, eslint, linter, javascript, nodejs]
comments: true
---

Lupakan sejenak mengenai _kaul one day one article_.  
Kini saya lebih tertarik menulis bot Telegram menggunakan javascript. Membuat bot Telegram bukanlah hal yang baru bagi saya, bisa dilihat di <https://github.com/rizaumami> yang isinya mayoritas repo bot Telegram. Namun, ada beberapa hal baru dari bot kali ini yang cukup membuat menarik:

1. `Javascript`, yang jarang atau sepertinya tidak pernah saya sentuh sebelumnya.
1. Ada beberapa layanan yang bisa digunakan untuk _deploy_ bot `javascript` secara gratis
1. Bot API. Agak aneh, walau saya sering berkutat dengan bot Telegram, namun jarang menyentuh bot API. _IMHO_, keunggulan bot API hanyalah _keyboard_ dan _inline mode_, di luar itu `telegram-cli` lebih unggul.

Tidak diragukan lagi, alasan kedua lah yang paling penting 😅  
Namun bukan itu yang akan dijadikan bahasan artikel kali ini. Dalam artikel yang lalu, saya pernah menulis [artikel mengenai `shellcheck`]({{ site.baseurl }}/2017/02/28/memeriksa-script-bash-menggunakan-shellcheck/) yang berguna membantu menulis skrip `bash` yang baik. Kali ini, tulisannya bertema bagaimana memasang _linter_ `eslint` di penyunting teks `geany`.

Hmmm.... Apa dan mengapa itu `eslint` dan `geany`?    
Saya bukanlah seorang _coder_. Menulis kode hanyalah sekedar _hobby_. Namun demikian, bukan berarti dalam menulis hanya asal skripnya jalan. Saya selalu berusaha membuat skrip yang baik, selain untuk memudahkan diri memahami cara kerja skrip tersebut, juga agar tidak menjadi cemoohan orang.

Di sinilah gunanya _linter_, untuk memeriksa skrip yang telah ditulis apakah telah memenuhi kaidah yang baik sesuai yang ditentukan. Sekilas mencari, didapati tiga _linter_ yang banyak dipakai untuk memeriksa `javascript`; (1) [`JSLint`](http://www.jslint.com/), (2) [`JSHint`](http://jshint.com/), dan (3) [`ESLint`](http://eslint.org/).  
Meski [`JSHint`](http://jshint.com/) paling populer, saya lebih memilih [`ESLint`](http://eslint.org/). Tidak ada alasan teknis, hanya saja [`JSHint`](http://jshint.com/) terasa kaku dan _strict_, tidak luwes.

Menggunakan [`ESLint`](http://eslint.org/) bisa melalui baris perintah di terminal, atau memasangnya sebagai _linter addons_ di penyunting teks. Namun sayangnya, `geany` tidak ada dalam [daftar penyunting teks](http://eslint.org/docs/user-guide/integrations) yang didukung [`ESLint`](http://eslint.org/).  
_But, worry not_, kita dapat menggunakan fitur **Set Build Commands** dari `geany` sebagai solusi. _Here is how...._

- [Pasang `ESLint`](http://eslint.org/docs/user-guide/getting-started). Dicontohkan secara global.

  ```bash
  sudo npm install -g eslint
  ```
- Pindah ke direktori proyek yang menggunakan `javascript`, misal `$HOME/jshitbot`.

  ```bash
  cd ~/jshitbot
  ```
- Buat berkas pengaturan.

  ```bash
  eslint --init
  ```
  
Sampai langkah ini, kita telah dapat menggunakan [`ESLint`](http://eslint.org/) melalui terminal.  
Dan langkah di bawah ini akan menyatukan [`ESLint`](http://eslint.org/) ke dalam `geany`.

- Jalankan `geany`, kemudian buka menu **Build ➜ Set Build Commands**.
- Dalam jendela **Set Build Commands** isi kolom:

  [![Set Build Commands di geany]({{ site.baseurl }}/assets/img/geany-eslint.png)]({{ site.baseurl }}/assets/img/geany-eslint.png)
  
  - **Label**. Bebas, misal dalam contoh gambar labelnya adalah ESLint.
  - **Command**. Isikan baris perintah berikut:
  
    ```
    eslint -f compact "%f"
    ```
  - **Working directory**. Isikan dengan _string_ `%d`.
  - Isi baris **Error regular expression** dengan baris berikut agar ketika baris _error_ diklik, kita diarahkan menuju baris bersangkutan:
  
    ```
    ([^:]+): line ([0-9]+), col ([0-9]+)
    ```
  - Pengaturan selesai. Tekan tombol **OK**.
  
Untuk menggunakannya, buka berkas `javascript` kemudian tekan tombol `F8`, atau melalui menu **Build ➜ ESLint** (misal ESLint adalah label yang kita pakai). Keluaran _linter_ akan muncul pada _Message Window_ `geany`. Klik pada baris _error_, kita pun akan diarahkan menuju baris bersangkutan dalam berkas `javascript` untuk melakukan perbaikan sesuai petunjuk _error_. 

_Code hard, code well...._

Sumber:
- <https://github.com/trongthanh/geany-for-front-end-dev/wiki/Using-JSHint-with-Geany>
- <https://www.sitepoint.com/comparison-javascript-linting-tools/>
