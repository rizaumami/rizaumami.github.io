---
layout: post
title: Compile Telegram CLI
tag: [compile, telegram, debian]
comments: true
---

*Why telegram-cli?* Ada begitu banyak Telegram *client*; dari Android, Windows Phone, Blackberry, Telegram Desktop, dan Telegram Web, *why telegram-cli?*

Tampilan spartan `telegram-cli` memang membuat jengah kita yang hendak bertelegram. Namun di samping tampilan jendela hitamnya, `telegram-cli` saya rasakan memiliki beberapa keunggulan dibanding Telegram *client* lain, misalnya lebih ringan dan gegas serta memiliki fitur yang tidak bisa didapat dari *client* lain misal diantaranya *history*, *stats*, dan *scriptable*.

`telegram-cli` tidak ada dalam repo Debian, jadi kita harus mengkompilasi mandiri. Langkah kompilasi dijabarkan jelas di laman [github `telegram-cli`](https://github.com/vysheng/tg). Namun jika Anda berminat mengkompilasi `telegram-cli` langsung menjadi paket `deb` agar bisa dipasang dan dikenali `package manager`, *follow this tut...*.

* Klon repo tg.  

{% highlight sh %}
git clone --recursive https://github.com/vysheng/tg.git && cd tg
{% endhighlight %}

* Pasang paket *dependency* bagi proses kompilasi.  

{% highlight sh %}
sudo aptitude install libreadline-dev libconfig-dev libssl-dev lua5.2 liblua5.2-dev libevent-dev libjansson-dev libpython-dev make 
{% endhighlight %}

* Berpindah ke dalam map repo hasil klon.  

{% highlight sh %}
cd tg
{% endhighlight %}

* Berkas `debian/changelog` tidak mencerminkan versi terkini dari `telegram-cli` sebagaimana tercantum dalam berkas `CHANGELOG`.  
Karenanya baik kita sunting berkas `debian/changelog` dari semula  

{% highlight sh %}
telegram-cli (1.0.6-1) unstable; urgency=low

  * Upstream update version
  * Inpliment -e option

 -- Steve Illichevsky <still.ru@gmail.com>  Sat, 11 Oct 2014 08:47:07 +0400

 telegram-cli (1.0.5.1-1) unstable; urgency=low

  * Initial release

 -- Steve Illichevsky <still.ru@gmail.com>  Sun, 05 Oct 2014 09:37:06 +0400
{% endhighlight %}  

Menjadi, misalnya

{% highlight sh %}
telegram-cli (1.3.3) unstable; urgency=low

  * support for sending custom keyboard 
  * fixed contact_search
  * TGL-2.0.3

 -- Sahri Riza Umami <riza@openmailbox.org>  Fri, 20 Nov 2015 16:16:28 +0700
{% endhighlight %}

* Mulai proses kompilasi.

{% highlight sh %}
dpkg-buildpackage -uc -us -b -rfakeroot
{% endhighlight %}

* Jika kompilasi lancar, akan menghasilkan berkas `telegram-cli_1.3.3_amd64.deb` di luar map `tg`.  

Pasang `telegram-cli` menggunakan perintah:

{% highlight sh %}
sudo dpkg -i telegram-cli_1.3.3-1_amd64.deb
{% endhighlight %}

*Let's telegramming like a 1337...* :sweat_smile:
