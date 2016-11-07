---
layout: post
title: Install ArchLinux di ThinkPad T520
tags: [archlinux, install, thinkpad]
comments: true
---

Setelah kemarin memasang Debian melalui Arch menggunakan cara `debootstrap`, ternyata Debian, bahkan Sid, sedang *freezing* karena semua *dev* berfokus pada persiapan kelahiran Jessie.

Saya lebih senang mendapatkan paket terkini daripada menunggu sistem stabil, jadi Arch kembali menjadi pilihan. Berikut adalah *log* pemasangan ArchLinux melalui Debian Sid.

Langkah pertama tentu mendapatkan media untuk pemasangan, bisa berupa berkas ISO atau citra *bootstrap*. Berkas ISO berukuran lumayan besar dan kita harus tetap melakukan *update*, jadi daripada membuang *bandwidth* lebih baik kita mengunduh citra `bootstrap` yang berukuran lebih kecil.

{% highlight sh %}
wget -c http://suro.ubaya.ac.id/archlinux/iso/2015.06.01/archlinux-bootstrap-2015.06.01-x86_64.tar.gz
{% endhighlight %}

Semua langkah di bawah lakukan sebagai *root*

{% highlight sh %}
sudo su
{% endhighlight %}

Salin berkas bootstrap ke tempat sementara, misal `/tmp`
Kita bisa saja melakukan `debootstrap` kedalam sebuah partisi atau *sub volume*.

{% highlight sh %}
cp archlinux-bootstrap-2015.06.01-x86_64.tar.gz /tmp
{% endhighlight %}

Berpindah ke `/tmp` dan uraikan (*extract*) berkas debootstrap

{% highlight sh %}
cd /tmp
tar xzf archlinux-bootstrap-2015.06.01-x86_64.tar.gz
{% endhighlight %}

Hasil ekstraksi akan berupa map bernama `root.x86_64`. Semua pengaturan akan dilakukan di/ke dalam map ini.

Paket dalam map `root.x86_64` hanya berupa paket-paket mendasar dan ada lebih banyak paket yang dibutuhkan untuk membangun sistem operasi yang nyaman bagi komputasi desktop perlu diambil dari internet. Karenanya kita perlu mengatur cermin (*mirror*) sebagai lumbung paket yang dibutuhkan. *Mirror* terdekat secara geografis akan memberikan kecepatan unduh terbaik.

{% highlight sh %}
nano /tmp/root.x86_64/etc/pacman.d/mirrorlist
{% endhighlight %}

Tekan `CTRL+W` dan ketik `Indonesia` untuk mencari baris *mirror* dari Indonesia, hilangkan tanda pagar di depan baris *mirror* yang diinginkan lalu tekan `CTRL+O` untuk menyimpan perubahan. Tekan `CTRL+X` untuk keluar dari `nano`.

Saatnya berpindah *root* untuk melakukan pengaturan dari dalam sistem `bootstrap`.

{% highlight sh %}
/tmp/root.x86_64/bin/arch-chroot /tmp/root.x86_64
{% endhighlight %}

Saatnya untuk memasang paket dasar sistem. Namun sebelumnya, kita mutakhirkan sistem yang ada.

{% highlight sh %}
pacman -Syu
{% endhighlight %}

Kemudian pasang paket dasar dan paket untuk keperluan *development*.

{% highlight sh %}
pacman -S base base-devel
{% endhighlight %}

Buat berkas *file system table*. Untungnya, Arch memiliki *script* khusus untuk tugas ini.

{% highlight sh %}
genfstab -U -p / > /etc/fstab
{% endhighlight %}

Karena kita berada dalam sistem `chroot` maka hasil `genfstab` memuat juga *mountpoint* lain dalam sistem yang (kemungkinan besar) tidak diperlukan. Sunting `/etc/fstab`:

{% highlight sh %}
nano /mnt/etc/fstab
{% endhighlight %}

Cukup sisakan baris yang diperlukan agar sistem lancar di-*boot*, misalnya `root (/)` dan `/home`.

Atur bahasa dan lokasi sebelum memasang paket apapun. Jika tidak, proses pemasangan akan dipenuhi komplain bahwa `locale` belum diatur dan ada kemungkinan pemasangan paket akan gagal.

{% highlight sh %}
nano /etc/locale.gen
{% endhighlight %}

Sunting berkas `locale.gen` dengan menghapus pagar di depan baris bahasa yang diinginkan. Meski kita berbahasa Indonesia, namun disarankan untuk tetap memilih juga bahasa Inggris; `id_ID.UTF-8` dan `en_US.UTF-8`.

{% highlight sh %}
locale-gen
echo LANG=en_US.UTF-8 > /etc/locale.conf
export LANG=en_US.UTF-8
{% endhighlight %}

Untuk pengaturan waktu, jika tidak tahu nama yang pas untuk daerah yang diinginkan, pakai perintah ini:

{% highlight sh %}
ls /usr/share/zoneinfo/
{% endhighlight %}

Cari daerah geografis di mana kita berada dan tentukan sebagai `localtime`.

{% highlight sh %}
ln -s /usr/share/zoneinfo/Asia/Jakarta /etc/localtime
{% endhighlight %}

Selanjutnya, atur *hardware clock*. Disarankan menggunakan `UTC` dibanding `localtime`.

{% highlight sh %}
hwclock --systohc --utc
{% endhighlight %}

Tentukan juga nama `host`.

{% highlight sh %}
echo arch > /etc/hostname
{% endhighlight %}

Meski menggunakan sistem 64 bit, ada kemungkinan kita perlu memasang paket yang hanya tersedia dalam 32 bit --misal `wine`--, karenanya baik kita aktifkan lumbung `multilib`.

{% highlight sh %}
nano /etc/pacman.conf
{% endhighlight %}

Hapus pagar di depan baris repo `multilib`.

{% highlight sh %}
[multilib]
Include = /etc/pacman.d/mirrorlist
{% endhighlight %}

Mutakhirkan *database* lokal menggunakan perintah:

{% highlight sh %}
pacman -Sy
{% endhighlight %}

Sebelum membuat akun *user* biasa, beri akun *root* sebuah *password*.

{% highlight sh %}
passwd
{% endhighlight %}

Kemudian, buat akun *user* biasa yang akan digunakan, dalam contoh *user* adalah `iza`.

{% highlight sh %}
useradd -m -g users -G wheel,storage,power -s /bin/bash iza
{% endhighlight %}

Tentukan juga kata kunci bagi *user* yang baru dibuat ini:

{% highlight sh %}
passwd iza
{% endhighlight %}

Beri *user* baru ini kemampuan `sudo` agar dapat melakukan pekerjaan yang membutuhkan hak *root*. *Install* paket `sudo`:

{% highlight sh %}
pacman -S sudo
{% endhighlight %}

Jalankan `visudo` untuk memasukkan *user* ke dalam daftar `sudoer`.  

{% highlight sh %}
EDITOR=nano visudo
{% endhighlight %}

Hapus pagar di depan baris:

{% highlight sh %}
%wheel ALL=(ALL) ALL
{% endhighlight %}

Bekerja dengan terminal akan lebih dimudahkan dengan bantuan `bash-completion` yang akan *auto-completing* perintah dan nama paket.

{% highlight sh %}
pacman -S bash-completion
{% endhighlight %}

Pasang *boot loader*. Karena sistem ini hanya berupa `bootstrap` dan tidak terletak dalam suatu partisi, kita hanya perlu memasang paket *bootloader* saja tanpa perlu menginstall *boot loader* ke *boot sector* partisi atau *disk*. 

*Install* `grub`:

{% highlight sh %}
pacman -S grub
{% endhighlight %}

Buat berkas konfigurasi `grub`.

{% highlight sh %}
grub-mkconfig -o /boot/grub/grub.cfg
{% endhighlight %}

Pasang `X` untuk dukungan grafis

{% highlight sh %}
pacman -S xorg
{% endhighlight %}

Pasang *driver* VGA. Karena ThinkPad saya ini hanya memiliki *integrated* VGA dari Intel, maka:

{% highlight sh %}
pacman -S xf86-video-intel
{% endhighlight %}

Laptop juga memerlukan *driver* masukan semacam *touchpad*:

{% highlight sh %}
pacman -S xf86-input-synaptics
{% endhighlight %}

Pasang *Desktop Environment*. Berikut cara memasang GNOME sebagai DE.

{% highlight sh %}
pacman -S gnome
{% endhighlight %}

Terdapat dua grup untuk GNOME, yaitu `gnome` dan `gnome-extra`. Pastikan `gnome` dipasang, `gnome-extra` hanya berupa paket tambahan. Jika menginginkan sistem yang lebih ramping, alih-alih memasang paket `gnome-extra`, kita dapat memilih untuk hanya memasang paket yang diperlukan saja.

Aktifkan layanan yang diperlukan, misal *login manager* dan *network manager*.

{% highlight sh %}
systemctl enable gdm.service
systemctl enable NetworkManager
systemctl start NetworkManager
{% endhighlight %}

Pemasangan sistem Arch dengan DE Gnome telah selesai dan siap disalin ke dalam partisi yang telah disiapkan. Saatnya keluar dari sistem `bootstrap`.

{% highlight sh %}
exit
{% endhighlight %}

Salin semua isi `/tmp/root.x86_64` ke dalam partisi yang telah disiapkan, misal `/dev/sda2` yang telah di-*mount* di `/mnt/hdd`.

{% highlight sh %}
rsync -aAXvh --progress /tmp/root.x86_64/* /mnt/hdd
{% endhighlight %}

Mutakhirkan `grub.cfg` *host* --dalam hal ini Debian Sid-- agar mengenali sistem operasi Arch

{% highlight sh %}
grub-update
{% endhighlight %}

Atau jika hendak menjadikan grub Arch sebagai *loader* utama, kita perlu melakukan `chroot` ke dalam partisi Arch dan memasang ke *disk* grub dari sana.

{% highlight sh %}
/mnt/hdd/bin/arch-chroot /mnt/hdd
grub-install --target=i386-pc --recheck /dev/sda
{% endhighlight %}

Keluar dari sistem `bootstrap` dan hidupkan ulang komputer untuk menjajal sistem anyar.

{% highlight sh %}
exit
reboot
{% endhighlight %}

