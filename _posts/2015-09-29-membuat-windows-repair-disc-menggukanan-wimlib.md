---
layout: post
title: Membuat Windows Repair Disc Menggunakan Wimlib
tags: [wimlib, windows, repair]
comments: true
---

*Shit happens!*
*Especially on Windows...*

Selalu sedia media untuk melakukan *backup* dan *recovery*. Media tersebut dapat dibuat melalui menu *Create a System Repair Disc* dalam Windows (7), atau dibuat menggunakan `wimlib` sebagaimana akan dirinci dalam artikel ini.

1. Install [wimlib](http://rizaumami.github.io/2015/07/09/compile-wimlib-di-debian-sid/).
2. Kaitkan berkas ISO Windows, misal di `/mnt/iso`.

{% highlight sh %}
sudo mount Windows7_Ultimate_x64_SP1.iso /mnt/iso/
{% endhighlight %}

Gunakan *script* `mkwinpeimg` dari `wimlib` untuk membuat berkas `WinPE`.

{% highlight sh %}
mkwinpeimg -i -a amd64 -W /mnt/iso /tmp/Repair_disc_Windows_7_64-bit.iso
{% endhighlight %}

Perintah di atas mungkin akan terhenti karena galat `mkwinpeimg` yang membutuhkan `mkisofs` yang tidak ada dalam repo Sid ketika artikel ini ditulis. Solusinya kita buat *softlink* `mkisofs` ke `genisoimage`.

{% highlight sh %}
sudo ln -s /usr/bin/genisoimage /usr/bin/mkisofs
{% endhighlight %}

Kemudian jalankan ulang perintah `mkwinpeimg` di atas.

*That's it*. Berkas `Repair_disc_Windows_7_64-bit.iso` yang berhasil dibuat dapat di `dd` atau *burn* ke cakram optik sebagai media *backup* dan *recovery*.

Jika ternyata *tools* dan *utility* yang terdapat dalam *repair disc* tersebut dirasa tidak memadai, kita dapat menyisipkan aplikasi lain ke dalamnya. Usahakan aplikasi tersebut *portable*, tidak membutuhkan installasi dan modifikasi *registry*.

Berkas ditambahkan ke `boot.wim` dengan cara *overlay*, jadi pastikan susunan direktorinya sama. Misal kita hendak menambahkan [BOOTICEx64.exe](http://bbs.wuyou.net/forum.php?mod=viewthread&tid=57675&extra=page%3D1&page=1) ke map `System32` dalam `boot.wim`, maka kita mesti menempatkan berkas `BOOTICEx64.exe` dalam susunan `OVERLAY/Windows/System32`:

1. Jalankan langkah 1-2 di atas.
2. Gunakan perintah berikut untuk melakukan *overlay*.

{% highlight sh %}
mkwinpeimg -i -a amd64 -W /mnt/iso -O /tmp/OVERLAY /tmp/Repair_disc_Windows_7_64-bit.iso
{% endhighlight %}

*Done!*

*Boot* dan masuk ke Windows *recovery environment*, jalankan `cmd`, ketik `booticex64.exe` kemudian tekan `ENTER`, maka `BOOTICEx64.exe` siap digunakan.


