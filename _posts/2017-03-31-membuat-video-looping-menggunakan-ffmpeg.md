---
layout: post
title: Membuat Video Looping Menggunakan ffmpeg
tags: [ffmpeg]
comments: true
---

Artikel kali ini akan singkat saja, demi tidak memutus kaul _one day one article_.  
Tadinya judul akan sepenuhnya berbahasa Indonesia, namun apa daya saya tidak bisa menemukan terjemah yang mengena untuk _video looping_.  

Ilham untuk artikel ini didapat dari grup [Telegram](https://telegram.org/) [PegeLinux](https://t.me/joinchat/AAAAAADqyq7cDPxcoNpkMA). Dalam grup tersebut sering dipertukarkan gambar bergerak yang _looping_ (maju-mundur, setelah sampai akhir gambar, balik ke awal, kemudian kembali ke akhir, demikian seterusnya tiada berujung).

Meski format gambar bergerak ini sering disebut sebagai [gif](https://en.wikipedia.org/wiki/Gif), namun nyatanya adalah video dalam bentuk [mpeg-4](https://en.wikipedia.org/wiki/Mpeg4). Jadi, Anda jangan bingung melihat judul tertera video, namun malah mengulas gambar bergerak. Karena pada dasarnya, [gif](https://en.wikipedia.org/wiki/Gif) dalam Telegram adalah video [mp4](https://en.wikipedia.org/wiki/Mpeg4) tanpa suara.

Baiklah, saya langsung saja menjabarkan bagaimana membuat video biasa menjadi _looping_ (berulang?).  
Misal kita mempunyai sebuah video berjudul anu.mp4 yang berdurasi 5 detik dan fps 25 _frame per second_. _Wait_, dari mana kita tahu semua angka ini? _Well_, Anda bisa menggunakan `ffprobe` atau piranti lunak lain yang dapat mengungkap _properties_ sebuah berkas multimedia. Nah, kita menginginkan video tersebut untuk _looping_ selama 60 detik. _Here's the magick_.

```bash
ffmpeg -i input.mp4 -filter_complex "[0]reverse[r];[0][r]concat,loop=5:250,setpts=N/25/TB" output.mp4  
```

Pada parameter `loop=5:250`, 5 artinya 5 kali _loop_, dan 250 adalah hasil kali _frame rate_ (25) dikalikan dengan nilai ganda panjang video (5 * 2 = 10 detik).

Sekarang, bagaimana jika kita menginginkan agar video tersebut _looping_ tiada akhir? Ini lebih gampang, cukup beri `loop` nilai 0.

```bash
ffmpeg -i input.mp4 -filter_complex "[0]reverse[r];[0][r]concat,loop=0,setpts=N/25/TB" output.mp4  
```

Akhirnya, Anda bisa mengunggah `output.mp4` ke Telegram untuk melakukan _shitposting_...

Wassalam...
