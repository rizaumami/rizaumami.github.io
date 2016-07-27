---
layout: post
title: Compile wimlib di Debian Sid
tags: [compile, debian, wimlib, ntfs]
comments: true
---

> Per-Februari 2016, `wimlib` telah masuk dalam repo Debian Sid dengan nama paket `wimtools`.
> Artikel ini tidak lagi diperlukan dan dibiarkan sebagai arsip.

`wimlib` adalah pustaka `C` untuk membuat, merubah, menguraikan, dan mengaitkan berkas [*Windows Imaging* (WIM)](https://en.wikipedia.org/wiki/Windows_Imaging_Format). `wimlib` dan antarmuka `wimlib-imagex` menyediakan alternatif gratis dan lintas *platform* bagi `WIMGAPI`, `ImageX`, dan `DISM` dari Microsoft. Untuk keterangan lebih lanjut, rujuk laman [http://wimlib.net/](http://wimlib.net/).

Untuk Unix/Linux, `wimlib` hanya disebarluaskan dalam bentuk *source tarball*. Berikut dicontohkan bagaimana mengkompilasi `wimblib` v1.8.2 dalam Debian Sid:

Unduh *source tarball* `wimlib` dari [http://wimlib.net/downloads/index.html](http://wimlib.net/downloads/index.html) dan uraikan menggunakan perintah uraikan `wimlib` (dicontohkan wimlib-1.8.3.tar.gz)

{% highlight sh %}
tar xvf wimlib-1.8.3.tar.gz
{% endhighlight %}

Masuk ke dalam map hasil uraian

{% highlight sh %}
cd wimlib-1.8.3
{% endhighlight %}

Sunting berkas `debian/changelog`

{% highlight sh %}
nano debian/changelog
{% endhighlight %}

Rubah baris 
    
{% highlight sh %}
wimlib (1.8.3-1) unstable; urgency=low
{% endhighlight %}
    
menjadi
    
{% highlight sh %}
wimlib (1.8.3) unstable; urgency=low
{% endhighlight %}
    
Pastikan *user* memiliki `PATH` `/sbin`, jika tidak kompilasi akan gagal karena gagal mejalankan `mkntfs` untuk pengujian biner hasil kompilasi.

{% highlight sh %}
export PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/sbin:/sbin:/opt/java/jre/bin:/usr/bin/vendor_perl:/usr/bin/core_perl"
{% endhighlight %}
    
Lakukan kompilasi. 

{% highlight sh %}
dpkg-buildpackage -uc -us -b -rfakeroot
{% endhighlight %}
    
Kompilasi mungkin akan terhenti karena tidak terpenuhinya paket yang dibutuhkan untuk kompilasi (*dependency*), misal:

{% highlight sh %}
dpkg-checkbuilddeps: Unmet build dependencies: debhelper (>= 8.9.7) autotools-dev pkg-config libfuse-dev libxml2-dev libssl-dev ntfs-3g-dev (>= 2011.4.12) attr-dev attr doxygen
{% endhighlight %}

Karenanya pasang dahulu paket-paket tersebut:

{% highlight sh %}
sudo aptitude install debhelper autotools-dev pkg-config libfuse-dev libxml2-dev libssl-dev ntfs-3g-dev attr-dev attr doxygen
{% endhighlight %}

Kemudian jalankan lagi perintah kompilasi.

{% highlight sh %}
dpkg-buildpackage -uc -us -b -rfakeroot
{% endhighlight %}

Berkas `.deb` hasil kompilasi berada sejajar map `wimlib-1.8.3` dan dapat dipasang ke sistem menggunakan `gdebi` atau perintah `dpkg -i *.deb`.
