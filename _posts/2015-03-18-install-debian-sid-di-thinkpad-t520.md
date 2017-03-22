---
layout: post
title: Debootstrapped Install Debian Sid di ThinkPad T520
tags: [debian, install, thinkpad]
comments: true
---

Tulisan ini merupakan catatan pemasangan installasi Debian Sid menggunakan debootstrap dari ArchLinux per Februari 2015.

`Debootstrap` adalah Debian _bootstrap_. Paket `debootstrap` tidak ada dalam repo ArchLinux melainkan dalam *Arch User Repository* (AUR). Pasang `debootstrap` menggunakan AUR *wrapper* semisal `yaourt`.

{% highlight sh %}
yaourt -S debootstrap
{% endhighlight %}

Lakukan `debootstrap`.

{% highlight sh %}
sudo debootstrap --arch=amd64 --variant=buildd --no-check-gpg sid /tmp/_sid http://kambing.ui.ac.id/debian  
{% endhighlight %}

Perintah di atas akan memasang sistem `debootstrap` Debian Sid 64 bit sekaligus paket `build-essential` ke map `/tmp/_sid`. Cermin yang digunakan adalah [http://kambing.ui.ac.id](http://kambing.ui.ac.id) dengan mengabaikan `gpg`.
 
Setelah sistem `debootstrap` lancar dipasang, kaitkan *virtual file system* agar sistem `debootstrap` terhubung ke *hardware*.

{% highlight sh %}
mount --bind /dev/ /tmp/_sid/dev
mount -t proc /proc /tmp/_sid/proc
mount -t sysfs /sys /tmp/_sid/sys
{% endhighlight %}

Sistem `debootstrap` yang terpasang belum memadai untuk pemakaian *desktop computing*, karenanya kita perlu memasang aplikasi-aplikasi yang diperlukan agar kegiatan berkomputasi menyenangkan.

Kaitkan `resolv.conf` *host* agar sistem `debootstrap` dapat menggunakan koneksi internet *host*.

{% highlight sh %}
mount --bind /etc/resolv.conf /tmp/_sid/etc/resolv.conf
{% endhighlight %}

Tentukan `/etc/hosts` bagi sistem `debootstrap`.  
Sunting berkas `/tmp/_sid/etc/hosts`:

{% highlight sh %}
nano /tmp/_sid/etc/hosts
{% endhighlight %}

Dan isikan baris berikut dengan --misal-- _hosts/hostname_ adalah `sid`:

{% highlight sh %}
127.0.0.1 localhost
127.0.1.1 sid

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
{% endhighlight %}

Tentukan juga nama `host`:

{% highlight sh %}
echo sid > /tmp/_sid/etc/hostname
{% endhighlight %}

Sistem `debootstrap` perlu mengenali dimana ia dipasang dan kandar mana sajakah yang terhubung padanya. Untuk inilah guna *filesystem table* dalam `/etc/fstab`.

Untuk mudahnya, `fstab` disalin dari sistem yang kini sedang dipakai.

{% highlight sh %}
cp /etc/fstab /tmp/_sid/etc
{% endhighlight %}

Pastikan untuk menyunting sesuai keadaan sistem yang baru. Kesalahan dalam langkah ini akan menyebabkan sistem gagal *boot* karena `bootloader` tidak mampu menemukan *root* sistem. Rujuk manual `fstab` untuk rinciannya.

Agar sistem yang kita pasang lebih ramping, baiknya kita matikan saran (*package recommendations*) dari APT hingga paket yang dipasang adalah hanya paket inti.

Gunakan penyunting teks untuk membuat berkas `/etc/apt/apt.conf.d/10recommends`:

{% highlight sh %}
nano /tmp/_sid/etc/apt/apt.conf.d/10recommends
{% endhighlight %}

Dan isikan *rules* berikut:

{% highlight sh %}
APT "";
APT::Install-Recommends "false";
{% endhighlight %}

*Rules* di atas membuat APT hanya memasang paket yang diminta tanpa menyarankan paket (yang dianggap) mendukung. Jika tetap menginginkan rekomendasi dari APT, jalankan APT dengan *switch* `-r`.

Sistem `debootstrap` secara asali akan merujuk [debian.org](http://www.debian.org/) sebagai cermin (*mirror*). Cermin paket akan sangat bergantung pada letak geografis, semakin dekat ia dengan kita maka semakin cepat laju pengunduhan paketnya. Karenanya, baik kita sunting `/etc/apt/sources.list` agar merujuk kepada cermin di Indonesia, misalnya [http://kambing.ui.ac.id](http://kambing.ui.ac.id).

{% highlight sh %}
nano /tmp/_sid/etc/apt/sources.list
{% endhighlight %}

Masukan baris repo, misalnya kambing, dengan menambahkan bagian `contrib` dan `non-free`:

{% highlight sh %}
deb http://kambing.ui.ac.id/debian sid main contrib non-free
{% endhighlight %}

Sistem `debootstrap` telah siap untuk pengaturan lebih lanjut semisal pemasangan aplikasi. Untuk melakukan pengaturan lanjutan ini kita mesti berpindah *root* dari yang sekarang digunakan (ArchLinux) ke dalam sistem `debootstrap`. Proses ini dinamakan `chroot`.

{% highlight sh %}
LANG=C chroot /tmp/_sid /bin/bash
{% endhighlight %}
  
Karena ArchLinux dan Debian berbeda `$PATH`, maka export `PATH` yang biasa digunakan Debian.

{% highlight sh %}
export PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/sbin:/sbin:/opt/java/jre/bin:/usr/bin/vendor_perl:/usr/bin/core_perl"  
{% endhighlight %}

Mutakhirkan daftar paket:

{% highlight sh %}
apt-get update
{% endhighlight %}

Pasang paket-paket yang mendasar, misal:

* `policykit-1` untuk mengatur perijinan
* `apt-listbugs` agar kita waspada mengenai *bug* dalam suatu paket
* `locales` menyesuaikan bahasa dan penulisan sesuai wilayah
* `aptitude` lebih disarankan daripada `apt-get`
* `nano` untuk menyunting berkas

{% highlight sh %}
apt-get -q -y install aptitude nano policykit-1 apt-listbugs locales
{% endhighlight %}

`locales` memerlukan pengaturan lebih lanjut:

{% highlight sh %}
dpkg-reconfigure locales
{% endhighlight %}

Pilih 146 (`en_US.UTF-8 UTF-8`) dan 261 (`id_ID.UTF-8 UTF-8`)
Pilih `en_US.UTF-8 UTF-8` sebagai default

Pasang paket-paket pembangun *graphical user interface* (GUI). Saya lebih menyukai GNOME dibanding *desktop environment* (DE) lain, karenanya kali ini pun akan memasang DE GNOME 3.

Pasang paket pendukung perangkat keras grafik (Xorg) dan *firmware* radio.

{% highlight sh %}
aptitude install xorg xserver-xorg-video-intel xserver-xorg-input-evdev firmware-linux-nonfree firmware-iwlwifi  
{% endhighlight %}

Pasang GNOME dan pendukungnya:

{% highlight sh %}
aptitude install gnome-shell colord file gdm3 gnome-control-center gnome-session gstreamer1.0-plugins-base gstreamer1.0-plugins-good gtk2-engines-pixbuf hwdata nautilus pulseaudio gnome-terminal gedit iceweasel  
{% endhighlight %}

Buat `username` baru, misal `iza`.

{% highlight sh %}
adduser iza
{% endhighlight %}

Buat grup `admin`.

{% highlight sh %}
addgroup --system admin
{% endhighlight %}

Tambahkan `user` yang tadi baru kita buat kedalam grup admin.

{% highlight sh %}
addgroup iza admin
{% endhighlight %}

Tentukan *password* bagi `root`.

{% highlight sh %}
passwd root
{% endhighlight %}

Cari paket kernel.

{% highlight sh %}
aptitude search linux-image
{% endhighlight %}

Pasang kernel.

{% highlight sh %}
aptitude install linux-image-amd64
{% endhighlight %}

Pasang *bootloader*.

{% highlight sh %}
aptitude install grub2 grub-imageboot
{% endhighlight %}

*Install* `grub` di _Master Boot Record (MBR)_ jika hendak menjadikannya *bootloader* utama. *Install* `grub` di _Partition Boot Sector (PBR)_ jika tidak ingin menjadikannya sebagai *bootloader* utama.

Sistem `debootstrap` telah selesai disiapkan. Keluar dari sistem `debootstrap`.

{% highlight sh %}
exit
{% endhighlight %}

Lepaskan semua kaitan yang digunakan proses ini.

{% highlight sh %}
umount /tmp/_sid/{dev,proc,sys}
{% endhighlight %}

Salin semua isi `/tmp/_sid` kedalam partisi yang telah disiapkan, misal /dev/sda2 yang telah di-*mount* di `/mnt/hdd`.

{% highlight sh %}
rsync -aAXvh --progress /tmp/_sid/* /mnt/hdd
{% endhighlight %}
