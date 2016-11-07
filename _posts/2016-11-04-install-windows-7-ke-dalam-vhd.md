---
layout: post
title: Install Windows 7 ke Dalam VHD
tags: [install, windows, vhd, winntsetup, bootice, flash, xiaomi, mi 4c]
comments: true
---

Artikel ini akan menjabarkan bagaimana memasang Windows 7 ke dalam [VHD (_virtual hard disk_)](https://en.wikipedia.org/wiki/VHD_(file_format)), sebuah jawaban atas pertanyaan bagaimana memasang Windows secara _native_ tanpa harus membuat partisi khusus untuk installasi Windows.

Mengapa memasang Windows 7 di VHD?  
Pendek cerita, saya perlu untuk melakukan _flashing_ pada [Xiaomi Mi 4c](http://www.gsmarena.com/xiaomi_mi_4c-7512.php). Sialnya, umumnya sumber di www menunjukkan proses _flashing_ ini mesti menggunakan sistem operasi Microsoft Windows, padahal selama ini saya hanya _single booting_ Debian Sid dan tidak memiliki installasi Windows kecuali dalam VirtualBox. Saya tidak cukup berani untuk mencoba _flashing_ menggunakan VirtualBox, khawatir _overhead_ antara _handphone_ hingga VirtualBox membuat _flashing_ gagal dan _bricking_ hape baru ini.

Dalam ThinkPad T520 saya miliki terdapat 2 sarana penyimpanan, yang pertama sebuah SSD 256GB yang terletak dalam _main bay_, dan yang kedua berupa sebuah HDD 1TB yang terletak dalam _ultra bay_ (slot DVD yang dijadikan penyimpanan HDD). Keduanya hanya terdiri dari satu partisi; SSD satu partisi berbentuk Ext4, dan HDD satu partisi berbentuk NTFS.  
Karena GRUB tidak mampu _booting_ VHD, maka berkas VHD akan diletakkan di HDD dan _booted_ oleh _bootloader_ Windows yang ditanam di _master boot record_ HDD.

Ada banyak cara memasang Windows 7 di VHD, namun dalam artikel ini hanya akan ditunjukkan pemasangan menggunakan _installer_ standar tanpa bantuan _software_ lain. Keuntungan cara ini kita hanya perlu menyiapkan berkas ISO _installer_ Windows, _software_ untuk _booting_ berkas ISO tersebut melalui _flash disk_, dan tentu saja _flash disk_ untuk tempat _installer_ Windows. Kerugiannya adalah proses pemasangan Windows relatif lama dan mesti menjalankan `command.exe` untuk membuat VHD.

Jadi, mari sebuah siapkan berkas ISO _installer_ Windows 7, sebuah _flash disk_ yang sanggup menampung berkas ISO tadi (minimal 4GB dan disarankan 8GB), dan _software_ untuk membuat _bootable flash disk_.  
Patut diperhatikan, hanya Windows edisi Enterprise, Ultimate, Server, dan Embedded yang mampu _native boot_ menggunakan VHD.  
Jika semua bahan telah siap, lanjutkan ke langkah membuat _bootable flashdisk_. Gunakan [Yumi](http://www.pendrivelinux.com/yumi-multiboot-usb-creator/), [Rufus](http://rufus.akeo.ie/), [Easy2Boot](http://www.easy2boot.com/), atau _software_ sejenis untuk membuat _bootable flash disk_ yang mampu _booting installer_ Windows.

Setelah pembuatan _bootable flash disk_ selesai, kita mulai proses inatllasi Windows ke dalam VHD:

- Tetap tancapkan _flash disk_ di _port_ USB dan _reboot_ komputer untuk kemudian _boot_ dari _flash disk_ tersebut.
- Setelah beberapa saat, akan muncul jendela **Install Windows** yang meminta kita untuk memilih bahasa, waktu, dan _keyboard_. Jangan dahulu tekan tombol **Next**, melainkan tekan kunci _keyboard_ `SHIFT+F10` untuk memanggil jendela `cmd.exe`.  
Dalam jendela `cmd.exe` ini kita akan membuat VHD dan mengaitkannya untuk kemudian dijadikan sebagai partisi installasi Windows 7.  
Baris-baris perintah pada langkah berikut dilakukan di dalam jendela `cmd.exe`.
- Panggil `diskpart`, sebuah _disk partitioning utility_ Windows
{% highlight sh %}
DISKPART
{% endhighlight %}
- Lihat daftar _disk_ atau partisi yang dikenali Windows
{% highlight sh %}
list volume
{% endhighlight %}
- Seperti telah dijelaskan di paragraf terdahulu, di laptop ini terpasang satu SSD Ext4 dan satu HDD NTFS. Perhatikan dengan seksama hasil perintah `list volume` di atas, jangan sampai salah pilih _volume_.  
Pilih _volume_ (partisi) NTFS yang akan jadi tempat kita meletakkan VHD, misal dicontohkan di sini sebagai _volume_ 0.
{% highlight sh %}
select volume 0
{% endhighlight %}
- Buat partisi ini _bootable_
{% highlight sh %}
active
{% endhighlight %}
- Windows menggunakan _drive letter_ sebagai identifikasi partisi, dan umumnya installasi Windows selalu menggunakan huruf C.
{% highlight sh %}
assign letter=c
{% endhighlight %}
- Saatnya membuat berkas VHD. Menggunakan _installer_ normal, Windows membutuhkan partisi berukuran setidaknya 13GB. Sebuah _fixed_ VHD berukuran 17GB akan cukup untuk kasus saya, karena niatannya hanya untuk dipasangi _flashing software_ yang ukurannya tidaklah besar.
{% highlight sh %}
create vdisk file=c:\windows7.vhd maximum=17000 type=fixed
{% endhighlight %}
- Pilih VHD yang telah dibuat
{% highlight sh %}
select vdisk file=c:\windows7.vhd
{% endhighlight %}
- Kaitkan, agar isinya bisa diakses
{% highlight sh %}
attach vdisk
{% endhighlight %}
- Buat partisi di dalam VHD
{% highlight sh %}
create part primary
{% endhighlight %}
- Format partisi ini
{% highlight sh %}
format fs=ntfs label="Windows 7" quick
{% endhighlight %}
- Jadikan partisi ini _bootable_
{% highlight sh %}
active
{% endhighlight %}
- Berikan partisi ini _drive letter_
{% highlight sh %}
assign
{% endhighlight %}
- Pembuatan VHD telah selesai, saatnya keluar dari `diskpart`
{% highlight sh %}
exit
{% endhighlight %}
- Tutup jendela `cmd.exe` karena diperlukan lagi.
{% highlight sh %}
exit
{% endhighlight %}

Pemasangan Windows dapat dilanjutkan sebagaimana kita biasanya memasang Windows, dengan satu catatan, pilih jenis pemasangan **Custom (advanced)** dan pilih partisi VHD sebagai target pada jendela _Where do you want to install Windows?_.

_That's it..._  
Tinggal tunggu beberapa saat dan beberapa kali _reboot_ sampai Windows benar-benar bisa digunakan.

```bash
format fs=ntfs label="Windows 7" quick
```

Jika menginginkan installasi yang lebih singkat, atau enggan mengetik perintah-perintah, kita bisa menggunakan bantuan [WinNTSetup](http://www.msfn.org/board/topic/149612-winntsetup-v386) dan [BootICE](http://bbs.wuyou.net/forum.php?mod=viewthread&tid=57675).

- Letakkan WinNTSetup dan BootICE di partisi NTFS, FAT, atau exFAT agar dapat dipanggil melalui `cmd.exe`  
- Gunakan BootICE untuk memasang _master_ atau _partition boot record_ dan membuat partisi _bootable_ (_active_)  
- Buat VHD dan install Windows menggunakan WinNTSetup  
- _reboot_  
- _profit!_ 

