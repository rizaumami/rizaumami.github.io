---
layout: post
title: Jangan Copy Paste dari Website ke Terminal!
tags: [terminal]
comments: true
---

Suatu malam, selepas melakukan _update_, mendadadak sistem Linux Anda menjadi bermasalah. Dan ternyata galat ini lumayan pelik, entah berapa situs dari hasil pencarian yang dikunjungi demi mendapat solusi. Sampai akhirnya Anda pun menemukan sebuah laman yang sepertinya memberi solusi. Dalam laman tersebut tertulis bahwa masalah dapat diselesaikan dengan menjalankan perintah yang tertera. Namun perintah tersebut lumayan panjang dan sulit diingat. Ngantuk, lelah, kesal, dan tidak sabar, Anda pun menyalin baris perintah tersebut dengan niat melekatkannya (_paste_) di terminal untuk kemudian dijalankan. 

_STOP RIGHT THERE MISTER!_

Jangan pernah menyalin baris perintah dari internet dan kemudian langsung melekatkannya ke dalam terminal!  
Mengapa? Karena bisa saja ada kode jahat tersembunyi di balik baris perintah tersebut.

Mari saya contohkan. Silakan salin perintah berikut: <code class="highlighter-rouge">
  <!-- He he... -->
  git clone
  <span style="position: absolute; left: -100px; top: -100px">/dev/null; clear; echo -n "Hai ";whoami|tr -d '\n';echo -e '!\nJANGAN PERNAH MENYALIN KODE DARI SEMBARANG SITUS WEB KE TERMINAL!<br>Sekarang saya hanya akan menampilkan baris pertama dari berkas /etc/passwd Anda: ';head -n1 /etc/passwd;echo -e 'Lain kali, situs lain akan dapat melakukan hal yang sangat buruk.'<br>git clone </span>
  https://github.com/rizaumami/rizaumami.github.io
</code>, kemudian lekatkan ke dalam terminal. Tidak ada yang aneh dari perintah itu kan? Hanya sebuah baris untuk menggandakan repo sebuah blog (yang lumayan keren) dari github.
 
Apa yang terjadi? Tidak berbahaya kan?  
Memang tidak berbahaya, karena ini hanyalah sebuah demo. Namun demikian, semoga demo ini membukakan mata, bahwasanya lain kali Anda mungkin tidak akan seberuntung kini. Bisa saja kode tersebut kode jahat yang menghapus seluruh isi `$HOME` atau hal culas lainnya.

Sebagai pencegahan, jika kita perlu untuk menyalin baris perintah dari internet, selalu lekatkan terlebih dahulu ke dalam penyunting teks. Dengan demikian kita bisa menelaah isinya dan mungkin memperbaiki kesalahan di dalamnya.

Sumber: [https://thejh.net/misc/website-terminal-copy-paste](https://thejh.net/misc/website-terminal-copy-paste)
