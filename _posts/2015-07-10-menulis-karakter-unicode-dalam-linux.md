---
layout: post
title: Menulis Karakter Unicode dalam Linux
tag: [linux, unicode]
comments: true
---

Pernah kerepotan ketika hendak menulis 10°, ¥500, 25², µm, ☺, dan lainnya? Simbol derajat, Yen, kuadrat, mu (mikro), dan emoji tidak tercakup dalam standar [ASCII](https://en.wikipedia.org/wiki/ASCII) melainkan [Unicode](https://en.wikipedia.org/wiki/Latin-1_Supplement_unicode_block), dan karena *keyboard* laptop yang umumnya kita gunakan adalah tipe US maka banyak karakter Unicode yang tidak bisa langsung diketik.

Berikut cara menuliskan karakter Unicode dalam Linux:

Tekan bersamaan kunci `CTRL` dan `SHIFT` sambil mengetik kode Unicode. Dari mana kita bisa tahu kode Unicode? Bisa dari aplikasi `gucharmap` atau dengan melihat tabel di [Latin-1_Supplement_unicode_block](http://en.wikipedia.org/wiki/Latin-1_Supplement_unicode_block), [List_of_Unicode_characters](http://en.wikipedia.org/wiki/List_of_Unicode_characters), dan tabel [emoji](https://en.wikipedia.org/wiki/Emoji).

Misal untuk menulis simbol derajat, berarti kita harus menahan kunci `CTRL` dan `SHIFT` sambil mengetikkan huruf `u00b0`, baru kemudian melepaskan `CTRL` dan `SHIFT`.

Atau jika kita hendak menuliskan emoji ☺, maka kita harus menahan kunci `CTRL` dan `SHIFT` sambil mengetikkan huruf `u263xa`, baru kemudian melepaskan `CTRL` dan `SHIFT`.

Mudah kan? ¯\\_(ツ)_/¯
