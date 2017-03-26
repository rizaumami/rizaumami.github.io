---
layout: post
title: Optimasi Blog
tags: [minify, optimasi, compress, javascript, css, html]
comments: true
---

Hmmm... Kali ini pun gagal melaksanakan _kaul_ _one day one article_ 🙈  
_So, here I am, sitting and read more [motivational quotes about writing](http://www.minterest.org/writing-quotes-101/)..._

> "You can always edit a bad page. You can't edit a blank page."  
> -- Jodi Picoult

> "Start where you are. Use what you have. Do what you can."  
> -- Arthur Ashe

> "Done is better than perfect."  
> -- Sheryl Sandberg

_Yeah, that's enough..._  

> Menulis sekarang juga, walau jelek tidak masalah karena bisa disunting di kemudian hari, daripada berangan membuat artikel sempurna dan menunggu inspirasi yang entah kapan datangnya.  
> -- Me

Artikel kali ini akan membahas bagaimana menjadikan blog ini lebih gegas dibuka. Walau pada dasarnya blog ini menggunakan tema asali dari [`jekyll`](https://jekyllrb.com/) yang lumayan ringan, ternyata menurut perbincangan dengan kawan-kawan di grup Telegram [PegeLinux](https://t.me/joinchat/AAAAAADqyq7cDPxcoNpkMA), masih bisa dijadikan lebih ringan dengan cara memampatkan _script_ (`CSS`, `javascript`) dan berkas `HTML`-nya.

Teorinya, sebuah laman web terdiri berkas `HTML` utama dan skrip-skrip pendukung. Ketika kita membuka laman web, pada dasarnya kita mengunduh berkas-berkas tersebut ke komputer kita dan membukanya menggunakan peramban web.  
Berkas yang dimampatkan akan memiliki ukuran yang kecil, sehingga lebih cepat diunduh dan menjadikan laman web kita lebih gegas dibuka.

_Let's see how..._

### Mampatkan _script_

Blog ini tidak menggunakan banyak skrip, hanya `CSS` sebagai pengatur tampilan mendasar, dan `javascript` untuk menyesuaikan tampilan pada perangkat berlayar kecil. Anda dapat mematikan `javascript` dan tetap bisa menikmati blog ini seutuhnya.

Awalnya, saya mencoba menggunakan _plugin_ [`jekyll`](https://jekyllrb.com/) agar proses pemampatan bisa otomatis terjadi ketika situs _rebuild_. Namun dari beragam cara dari internet, misal dari laman [Stack OverFlow](https://stackoverflow.com/questions/10208481/how-to-compress-and-minify-assets-using-jekyll-plugins) dan artikel [Jekyll Compressing](https://www.ikiapps.com/tips/2015/12/27/jekyll-compressing) tidak kunjung membuahkan hasil, akhirnya saya memampatkan berkas `CSS` dan `javascript` mandiri menggunakan bantuan laman [CSS Compressor](http://csscompressor.com/).

Berkas `CSS` hasil pemampatan tersebut sebenarnya masih bisa dibuat lebih ringkas menggunakan bantuan laman [unCSS Online!](https://uncss-online.com/) untuk membuang bagian-bagian yang tidak terpakai oleh laman blog ini. Namun saya dapati, proses ini lumayan membosankan karena mesti berulang-kali memastikan bahwa tidak ada laman yang rusak karena `CSS`-nya terbuang.

Sebagai acuan, berkas `CSS` yang tadinya berukuran sekitar 8KB dapat dimampatkan oleh [CSS Compressor](http://csscompressor.com/) menjadi sekitar 5,3KB. Pada percobaan menggunakan [unCSS Online!](https://uncss-online.com/) didapati hasil akhir sekitar 2,5KB.

### Mampatkan berkas HTML

Jika Anda rajin menelusuri blog ini sampai artikel lampau, tentunya akan jarang atau mungkin tidak mendapati artikel yang panjang. Walau demikian, jika masih bisa dijadikan ringkas, mengapa tidak?

Blog ini menggunakan [`jekyll`](https://jekyllrb.com/) di [GitHub Pages](https://pages.github.com/), yang membangun laman HTML secara dinamis tiap kali dilakukan _push_ ke [GitHub Pages](https://pages.github.com/). Karenanya kita tidak bisa memampatkan berkas HTML sebagaimana memampatkan berkas `CSS` dan `javascript` di atas.  

Beruntung, caranya ternyata lebih mudah:

- Kunjungi laman [https://github.com/penibelst/jekyll-compress-html](https://github.com/penibelst/jekyll-compress-html) dan unduh [rilis](https://github.com/penibelst/jekyll-compress-html/releases) terakhirnya.
- Uraikan berkas `compress.html` ke map `_layouts` dalam blog repo.
- Sunting `_layouts/default.html` untuk menambahkan baris berikut:

  ```html
  ---
  layout: compress
  ---
  ```
  
- Sunting berkas `_config.yml` dan tambahkan baris berikut:

  ```yaml
  compress_html:
    clippings: all
    comments:  ["<!-- ", " -->"]
    endings:   all
  ```
  
- _That's it!_. Kita bisa langsung _push_ dan GitHub akan membuat ulang berkas HTML yang lebih ringkas. 
  
Sekian dulu, dan jangan lupa untuk mengunjungi laman-laman berikut yang menjadi sumber bagi artikel ini:

- [linhub.io](https://linhub.io//2017/02/17/optimasi-linhubio-part-2)  
- [https://github.com/penibelst/jekyll-compress-html](https://github.com/penibelst/jekyll-compress-html)
- [http://rich-knight.com/articles/compressing-html-in-jekyll/](http://rich-knight.com/articles/compressing-html-in-jekyll/)
