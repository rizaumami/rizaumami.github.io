---
layout: post
title: Memperkaya Suara di Linux Menggunakan pulseaudio-equalizer
tags: [pulseaudio, equalizer]
comments: true
---

_Welp_, niatan _one-day-one-article_ ternyata tidak berjalan mulus. Baru menulis dua artikel saya telah kehabisan ide tulisan. _But, never give up!_

Artikel kali ini akan membahas mengenai `pulseaudio-equalizer`, sebuah paket yang menyediakan _equalizer sink_ dan `qpaeq` (GUI _equalizer_).  

Jadi, apa itu _equalizer_? Sesuai namanya, _equalizer_ adalah sebuah alat untuk menyesuaikan tanggapan frekuensi suara sesuai keadaan idealnya. Kalimat keadaan ideal ini kemudian memicu berbagai silang pendapat mengenai bagaimanakah rupa suara yang ideal itu?  
Gambaran sederhananya, ketika seorang musisi menggubah musik dan kemudian mewujudkan musik tersebut ke sebuah media rekam untuk kemudian disebarkan ke para penikmat musiknya, akan ada banyak faktor yang memengaruhi musik tersebut, yang kemungkinan besar rupa musik yang sampai di penikmat musiknya tidak lagi sesuai dengan apa yang ada dalam benaknya atau musik yang dia dengar di studio rekaman.  

Ada kalangan yang bersikeras bahwa musik itu harus didengarkan _flat_ tanpa campur tangan _tone control_ atau _equalizer_ karena alat tersebut akan menambahkan "noda" pada musik, dan menjadikan musik tidak lagi murni sesuai kehendak si penciptanya.  
Nyatanya, penampilan sebuah rekaman akan dipengaruhi oleh cara merekam, alat rekam, monitor rekaman, sarana penyebaran rekaman, alat pemutar rekaman, dan bahkan telinga si pendengar rekaman.  
Dari sini kita mendapat kesan, bahwasanya tidak ada rekaman yang murni. Untuk apa kita bersikeras mendengarkan musik jika kita tidak dapat menikmati nada tertentu karena misalnya telinga kita yang sudah tua, atau karena alat pemutar rekaman yang tidak berkualitas _high-end_, atau sebab lainnya. Musik adalah untuk dinikmati, jangan jadikan keterbatasan alat atau indera pendengaran mengganggu kesempatan kita menikmati musik.  
Di sinilah gunanya _equalizer_, untuk memberikan kompensasi dari kekurangan-kekurangan yang dimiliki dalam menikmati musik. Jika ternyata _speaker_ atau _headphone_ yang dimiliki terlalu lantang di nada tengah, kita bisa gunakan _equalizer_ untuk menurunkan penguatan di nada tengah, kalau misal telinga kita sudah tidak terlalu peka dalam mendengar nada tinggi, kita gunakan _equalizer_ untuk memberikan penguatan lebih pada nada tinggi.

Lalu, bagaimana kita bisa memasang _equalizer_ dalam sistem Linux? Maklum, sekarang komputer tidak lagi sekedar alat bekerja, namun juga sarana hiburan.  
Ada dua cara memasang _equalizer_, pertama adalah lokal, per aplikasi, misal _plugin_ untuk `rhythmbox`, dan cara kedua adalah global, _system wide_, misal pada `pulseaudio` yang bekerja sebagai _sound server_ di mayoritas distro Linux modern.

Seperti yang telah ditulis di awal, kali ini saya lebih memilih untuk memasang _equalizer_ untuk `pulseaudio`. _Here's how..._

- `pulseaudio-equalizer` sudah ada dalam repo Debian, jadi bisa langsung dipasang menggunakan perintah:

  ```sh
  sudo aptitude install pulseaudio-equalizer
  ```

- Namun tidak akan bisa langsung digunakan karena beberapa modul yang diperlukannya belum dimuat, karenanya muat modul-modul tersebut:

  ```sh
  pactl load-module module-equalizer-sink
  pactl load-module module-dbus-protocol
  ```

  Modul-modul tersebut mesti kita muat tiap kali sistem _rebooted_. Berikut agar kita tidak perlu repot memuat ulang modul-modul tersebut secara manual.

  ```sh
  sudo nano /etc/pulse/default.pa
  ```

  Dan tambahkan baris berikut di akhir berkas:

  ```sh
  load-module module-equalizer-sink
  load-module module-dbus-protocol
  ```

- Sekarang, kita bisa memanggil `qpaeq`, sebuah antarmuka grafis untuk mengatur _equalizer_. Sepertinya tidak ada _launcher_ untuk memanggil `qpaeq` dari selain terminal, jadi ketik perintah berikut di terminal:

  ```sh
  qpaeq
  ```

- _That's it_. Mainkan berkas audio dan atur tanggapan nadanya menggunakan `qpaeq`.

Oh ya, saat _googling_ mencari bahan untuk artikel ini, saya juga menemukan sebuah proyek _equalizer_ yang lebih sederhana dan langsung bekerja; [footswitch2 equaliser](https://sourceforge.net/projects/footswitch2equaliser).

{% highlight sh %}
{% endhighlight %}
