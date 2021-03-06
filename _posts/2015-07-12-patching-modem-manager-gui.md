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

  ```sh
  deb http://kambing.ui.ac.id/debian sid main contrib non-free
  ```

* Tambahkan baris untuk *source*:

  ```sh
  deb-src http://kambing.ui.ac.id/debian sid main contrib non-free
  ```

* Mutakhirkan *database* lokal

  ```sh
  sudo aptitude update
  ```

* Buat map tersendiri untuk proses kompilasi ini agar lebih rapi.

  ```sh
  mkdir mmgui
  ```

* Dan pindah ke dalamnya.

  ```sh
  cd mmgui
  ```

* Unduh *source* `modem-manager-gui`

  ```sh
  apt-get source modem-manager-gui
  ```

* Unduh *patch*.

  ```sh
  wget http://download.tuxfamily.org/gsf/patch/modem-manager-gui-0.0.17.1-fix-libebook-api-break-v2.patch  
  ```

* Uraikan berkas `modem-manager-gui_0.0.17.1.orig.tar.gz` menggunakan perintah:

  ```sh
  tar xvf modem-manager-gui_0.0.17.1.orig.tar.gz
  ```

* Uraikan berkas `modem-manager-gui_0.0.17.1-2.debian.tar.xz` ke dalam map `modem-manager-gui`.

  ```sh
  tar -xJC modem-manager-gui-0.0.17.1  -f modem-manager-gui_0.0.17.1-2.debian.tar.xz  
  ```

* Masuk ke dalam map `modem-manager-gui-0.0.17.1` untuk memulai kompilasi

  ```sh
  cd modem-manager-gui-0.0.17.1
  ```

* *Patch source*.

  ```sh
  patch -p1 < ../modem-manager-gui-0.0.17.1-fix-libebook-api-break-v2.patch
  ```

* Lakukan kompilasi

  ```sh
  dpkg-buildpackage -uc -us -rfakeroot
  ```

* Jika kompilasi terhenti karena galat:

  ```sh
  dpkg-checkbuilddeps: Unmet build dependencies: itstool libgdbm-dev libglib2.0-dev libgtk-3-dev ofono-dev (>= 1.9) po4a  
  ```

* Berarti ada *dependency* yang tidak terpenuhi. Install paket-paket ketergantungan tersebut.

  ```sh
  sudo aptitude install itstool libgdbm-dev libglib2.0-dev libgtk-3-dev ofono-dev po4a
  ```

  Ulangi kompilasi.

* Jika kompilasi berhasil, akan ada berkas `modem-manager-gui_0.0.17.1-2_amd64.deb` dan `modem-manager-gui-dbg_0.0.17.1-2_amd64.deb` di luar map `mmgui`.

* Install paket `modem-manager-gui` hasil kompilasi.

  ```sh
  modem-manager-gui-dbg_0.0.17.1-2_amd64.deb
  ```

![mmgui](http://i.imgur.com/3LyvSZ2.png)

*That's it!*, `modem-manager-gui` siap digunakan ... :satisfied:

