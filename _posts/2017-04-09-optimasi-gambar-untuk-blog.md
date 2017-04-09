---
layout: post
title: Optimasi Gambar untuk Blog
tags: [gimp]
comments: true
---

Jika Anda rajin membaca artikel di blog ini, tentunya mendapat bahwa jarang artikel yang terdapat gambar di dalamnya. Hal tersebut memang disengaja berdasar beberapa alasan. Alasan pertama, agar blog lebih gegas dimuat. Alasan kedua, saya lebih sering menulis perihal dunia GNU/Linux yang umumnya bisa disampaikan dengan baik walau hanya menggunakan kata. Dan alasan ketiga, saya malas menyiapkan gambar pendukung artikel. Menulis pun sudah susah, terlebih jika mesti menyiapkan gambar...

_I rarely use images on my articles, but when I do, I do image optimization..._

Kadang, ada artikel yang tidak terelakkan mesti diimbuhi gambar pendukung. Untuk kasus demikian, saya usahakan gambarnya sesedikit mungkin dengan ukuran sekecil mungkin, agar pengalaman membaca blog tidak cedera karena mesti memuat gambar berjumlah banyak dan berukuran besar.  
Namun tentunya ukuran dan jumlah gambar bukan bakuan kaku untuk optimasi blog bergambar. Silakan rujuk artikel [Image Optimization](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/image-optimization) untuk mendapatkan pemahaman lebih mengenai upaya yang dapat dilakukan untuk optimasi gambar yang akan digunakan dalam sebuah laman internet, yang intisarinya dapat diurutkan sebagai berikut:

- **Gunakan format [_vector_](https://en.wikipedia.org/wiki/Vector_image)**  
  Gambar berbentuk [_vector_](https://en.wikipedia.org/wiki/Vector_image) adalah berbentuk kecil dan bisa diubah ukurannya tanpa mengganggu mutu gambar itu sendiri. 
- **Rampingkan dan mampatkan aset-aset [SVG](https://en.wikipedia.org/wiki/Svg)**  
  Jika membuat gambar [SVG](https://en.wikipedia.org/wiki/Svg) menggunakan _software_ pembantu, umumnya akan banyak _tag_ yang tidak berkaitan dengan gambar. Buang saja.  
- **Gunakan format gambar [_raster_](https://en.wikipedia.org/wiki/Raster_image) terbaik**  
  Ada banyak format gambar [_raster_](https://en.wikipedia.org/wiki/Raster_image), gunakan yang terbaik sesuai jenis gambar. Misal jika gambar berupa foto, yang memiliki banyak warna dan detil, gunakan JPEG. Sementara jika gambar memiliki sedikit warna, seperti ikon atau _screenshot_, maka format PNG lebih cocok.
- **Coba berbagai pengaturan gambar [_raster_](https://en.wikipedia.org/wiki/Raster_image)**  
  Atur format gambar untuk menghasilkan ukuran kecil tanpa --atau hanya sedikit-- kehilangan mutunya.
- **Buang metadata yang tidak penting dari gambar**  
  Anda tidak perlu _timestamp_ atau _geo-tagging_ pada gambar untuk blog, malah bisa menjadi celah keamanan dan _privacy_. 
- **Sesuaikan ukuran gambar**  
  Internet diakses oleh berbagai perangkat dengan ukuran layar yang berbeda. Kita tidak perlu menyediakan gambar dengan kerapatan tinggi jika target pembaca hanya memiliki layar berkerapatan 1366x768.

_Hmmm.... Seems complicated isn't it?_  

Dari enam saran _best practice_ di atas, semuanya mengerucut ke satu kaidah; gunakan ukuran gambar sekecil mungkin tanpa (atau sesedikit mungkin) kehilangan kualitasnya.  
Lalu bagaimana kita menghasilkan gambar berukuran kecil namun bermutu bagus? Nah, inilah tujuan artikel kali ini; menggunakan GIMP untuk mengolah gambar yang akan digunakan dalam blog. Apakah mesti menggunakan GIMP? Tidak harus, kita juga bisa menggunakan `imagemagick`. Namun GIMP lebih mudah, jadi mungkin mengolah gambar untuk web menggunakan `imagemagick` akan dibahas di lain kesempatan.

Berikut langkahnya:
- Alih-alih mengolah gambar secara manual, kita akan gunakan _extension_ `safe-for-web` yang akan melakukan tugas secara otomatis. `safe-for-web` dikemas dalam paket `gimp-plugin-registry`. Karenanya, langkah pertama adalah memasang paket tersebut.

  ```bash
  sudo aptitude install gimp-plugin-registry
  ```
- GIMP sudah siap mengolah gambar untuk _web_. Buka gambar yang hendak digunakan dalam blog, kemudian olah sesuai yang diinginkan.
- Setelah selesai disunting, jangan disimpan menggunakan cara biasa, melainkan melalui **File ➜ Save for Web...**  
  Kemudian akan muncul jendela berikut: 

  [![GIMP safe for web extension]({{ site.baseurl }}/assets/img/gimp-websave.png)]({{ site.baseurl }}/assets/img/gimp-websave.png)
  
  Pilih format gambar (**1**) dan pengaturan (**2**) agar menghasilkan ukuran gambar (**3**) paling kecil namun mutu gambar masih terjaga. Jika telah didapat kompromi yang sesuai, klik tombol **Save**, dan gambar siap digunakan dalam blog.
  






