---
layout: post
title: Patching ModemManager GUI
tag: [linux, modem, compile]
comments: true
---

Manusia lebih cepat belajar menggunakan indera penglihatan. Sebagai pengguna GNU/Linux, berinteraksi dengan antarmuka teks tidaklah aneh, namun adakalanya tampilan GUI (*graphical User Interface*) lebih diminati.

`mmcli` tidak diragukan kemampuannya, namun sifatnya sebagai aplikasi CLI membuatnya kurang kurang intuitif. Untungnya ada pihak yang mengembangkan GUI bagi `mmcli`; yakni `modem-manager-gui`.

Sayangya, `modem-manager-gui` v0.0.17.1-2 di Debian Sid selalu *crash* tiap kali dijalankan. Sudah ada *bug report* di [http://linuxonly.ru/cms/e107_plugins/bug_tracker/bugs.php?0.item.3.31.0](http://linuxonly.ru/cms/e107_plugins/bug_tracker/bugs.php?0.item.3.31.0) yang menyatakan; *crash* terjadi karena API *break* pada pustaka `libebook` v3.16. Pemecahannya? Dengan *downgrade* paket `evolution-data-server` ke versi 3.14 atau dengan membangun ulang `modem-manager-gui` dengan [*patch ini*](http://download.tuxfamily.org/gsf/patch/modem-manager-gui-0.0.17.1-fix-libebook-api-break-v2.patch).

Karena saya menggunakan DE Gnome, *downgrade* `evolution-data-server` tidak bisa diterima karena akan membuang `gdm3`, `gnome-session`, dan `gnome-shell` yang artinya membuang semua DE.

Pilihan kedua, yakni dengan membangun ulang `modem-manager-gui` dengan [*patch*](http://download.tuxfamily.org/gsf/patch/modem-manager-gui-0.0.17.1-fix-libebook-api-break-v2.patch)-lah yang bisa diterima dan yang akan dituliskan dalam artikel ini.

*Let's begin...*

* Unduh *source* `modem-manager-gui`. Cara yang disarankan tentu saja dengan mengaktifkan repo *source* pada `/etc/apt/sources.list`.
Sunting `/etc/apt/sources.list` menggunakan `nano` dengan perintah `sudo nano /etc/apt/sources.list`. Jika misalnya repo Anda adalah seperti berikut:

{% highlight sh %}
deb http://kambing.ui.ac.id/debian sid main contrib non-free
{% endhighlight %}

Tambahkan baris untuk *source*:

{% highlight sh %}
deb-src http://kambing.ui.ac.id/debian sid main contrib non-free
{% endhighlight %}

Mutakhirkan *database* lokal

{% highlight sh %}
sudo aptitude update
{% endhighlight %}

Buat map tersendiri untuk proses kompilasi ini agar lebih rapi.

{% highlight sh %}
mkdir mmgui
{% endhighlight %}

Dan pindah ke dalamnya.

{% highlight sh %}
cd mmgui
{% endhighlight %}

Unduh *source* `modem-manager-gui`

{% highlight sh %}
apt-get source modem-manager-gui
{% endhighlight %}

Unduh *patch*.

{% highlight sh %}
wget http://download.tuxfamily.org/gsf/patch/modem-manager-gui-0.0.17.1-fix-libebook-api-break-v2.patch
{% endhighlight %}

Uraikan berkas `modem-manager-gui_0.0.17.1.orig.tar.gz` menggunakan perintah:

{% highlight sh %}
tar xvf modem-manager-gui_0.0.17.1.orig.tar.gz
{% endhighlight %}

Uraikan berkas `modem-manager-gui_0.0.17.1-2.debian.tar.xz` ke dalam map `modem-manager-gui`.

{% highlight sh %}
tar -xJC modem-manager-gui-0.0.17.1  -f modem-manager-gui_0.0.17.1-2.debian.tar.xz
{% endhighlight %}

Masuk ke dalam map `modem-manager-gui-0.0.17.1` untuk memulai kompilasi

{% highlight sh %}
cd modem-manager-gui-0.0.17.1
{% endhighlight %}

*Patch source*.

{% highlight sh %}
patch -p1 < ../modem-manager-gui-0.0.17.1-fix-libebook-api-break-v2.patch
{% endhighlight %}

Lakukan kompilasi

{% highlight sh %}
dpkg-buildpackage -uc -us -rfakeroot
{% endhighlight %}

Jika kompilasi terhenti karena galat:

{% highlight sh %}
dpkg-checkbuilddeps: Unmet build dependencies: itstool libgdbm-dev libglib2.0-dev libgtk-3-dev ofono-dev (>= 1.9) po4a
{% endhighlight %}

Berarti ada *dependency* yang tidak terpenuhi. Install paket-paket ketergantungan tersebut.

{% highlight sh %}
sudo aptitude install itstool libgdbm-dev libglib2.0-dev libgtk-3-dev ofono-dev po4a
{% endhighlight %}

Ulangi kompilasi.

Jika kompilasi berhasil, akan ada berkas `modem-manager-gui_0.0.17.1-2_amd64.deb` dan `modem-manager-gui-dbg_0.0.17.1-2_amd64.deb` di luar map `mmgui`.

Install paket `modem-manager-gui` hasil komppilasi.

{% highlight sh %}
modem-manager-gui-dbg_0.0.17.1-2_amd64.deb
{% endhighlight %}

![mmgui](http://i.imgur.com/3LyvSZ2.png)

*That's it!*, `modem-manager-gui` siap digunakan ... :satisfied:

