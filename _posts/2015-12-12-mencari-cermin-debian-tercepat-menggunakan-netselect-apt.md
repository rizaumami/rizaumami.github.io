---
layout: post
title: Mencari Cermin Debian Tercepat Menggunakan netselect-apt
tags: [netselect-apt,sources.list,mirror,debian]
comments: true
---

*Alhamdulillah*, musim hujan telah tiba.
*But, then shit happens*. Musim hujan musim mati lampu. Sinyal seluler raib jika mati lampu. Dan peradaban pun terhenti ...

Artikel ini sedikit bahkan mungkin tidak ada kaitannya dengan paragraf di atas, *I just need to vent that out ...* :smiley:

Akhir-akhir ini cermin (*mirror*) lumbung paket dalam negeri sering tidak dapat diandalkan, entah itu [kambing](http://kambing.ui.ac.id/debian/) atau [suro.ubaya](http://suro.ubaya.ac.id/debian/) sering tidak dapat terhubung atau paket-paketnya telah kadaluwarsa. Mau tidak mau mesti berganti cermin, *but, how?*

Pengguna Debian dapat dengan mudah membuat `sources.list` berisi cermin tercepat menggunakan `netselect-apt`. Berikut dicontohkan menggunakan `netselect-apt` untuk membuat `sources.list` berisi cermin dari Indonesia untuk Debian:

* Pasang paket `netselect-apt`.  {% highlight sh %}
  sudo aptitude install netselect-apt
  {% endhighlight %}

* Jalankan `netselect-apt` untuk mendaftar cermin tercepat.  {% highlight sh %}
  sudo netselect-apt sid -nc ID -o /etc/apt/sources.list
  {% endhighlight %}  Yang artinya; jalankan `netselect-apt` untuk mencari cermin Debian `sid` tercepat di Indonesia (`-c ID`) dan simpan hasilnya di `/etc/apt/sources.list`, tidak lupa untuk memasukkan komponen *non-free* (`-n`) ke dalamnya.  
Berikut hasilnya;  {% highlight sh %}
Using distribution sid.
Retrieving the list of mirrors from www.debian.org...  
--2015-12-12 08:12:45--  http://www.debian.org/mirror/mirrors_full
Resolving www.debian.org (www.debian.org)... 128.31.0.62, 140.211.15.34
Connecting to www.debian.org (www.debian.org)|128.31.0.62|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 366123 (358K) [text/html]
Saving to: ‘/tmp/netselect-apt.oNP9Oc’  
/tmp/netselect-apt.oNP9O 100%[==================================>] 357.54K  68.8KB/s   in 5.2s  
2015-12-12 08:12:53 (68.8 KB/s) - ‘/tmp/netselect-apt.oNP9Oc’ saved [366123/366123]  
Choosing a main Debian mirror using netselect.
(will filter only for mirrors in country ID)
Running netselect to choose 10 out of 7 addresses.
...................................................
Only found  4 hosts out of 10 requested.
The fastest 10 servers seem to be:  
  http://suro.ubaya.ac.id/debian/
  http://buaya.klas.or.id/debian/
  http://mirror.unej.ac.id/debian/
  http://kartolo.sby.datautama.net.id/debian/  
Of the hosts tested we choose the fastest valid for HTTP:  
        http://suro.ubaya.ac.id/debian/  
Writing /etc/apt/sources.list.
Done.
{% endhighlight %}

* *That's it!*
  Berkas `sources.list` kini berisi cermin tercepat yang dapat diakses dari tempat kita.  {% highlight sh %}
  # Debian packages for sid
  deb http://suro.ubaya.ac.id/debian/ sid main contrib non-free
  # Uncomment the deb-src line if you want 'apt-get source'
  # to work with most packages.
  # deb-src http://suro.ubaya.ac.id/debian/ sid main contrib non-free  
  # Security updates for stable
  # deb http://security.debian.org/ stable/updates main contrib non-free
  {% endhighlight %}

