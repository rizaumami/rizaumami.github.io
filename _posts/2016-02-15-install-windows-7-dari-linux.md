---
layout: post
title: Install Windows 7 Melalui Linux
tags: []
comments: true
---

Bukan. Artikel ini bukan akan mengupas bagaimana membuat _installer_ Windows melalui GNU/Linux. Melainkan akan menerangkan bagaimana memasang Windows 7 (juga 8, 8.1 dan 10) langsung dari dalam lingkungan sistem operasi GNU/Linux, khususnya Debian Sid.

Sebenarnya telah lama saya menggunakan cara ini. Pernah membuat _thread_-nya di Kaskus, juga artikel di beberapa blog lama (yang kini lumayan saya abaikan).
Sekarang saya akan menulis ulang artikel-artikel tersebut disini, karena kebetulan belum lama ini mesti memasang Windows di ThinkPad T420 dan lumayan kerepotan ketika menggunakan `easy2boot` dan `WinNTSetup3`.

_Here's my story_...

ThinkPad T420 ini akan digunakan bersama. Jadi saya tidak bisa memaksakan _single boot_ Linux karena dipastikan banyak yang akan mengeluh. Meski sebenarnya jika dipasang Windows pun pasti tetap akan ada yang _complain_ :D.
HDD telah diganti SSD dan diformat menjadi 4 partisi; 2 NTFS dan 2 ext4.

**Siapkan Prasyarat**

1. Berkas ISO Microsoft Windows 7.  
Sayangnya, setelah rilis Windows 10 kita tidak lagi bisa mengunduh berkas ISO Microsoft Windows 7 secara resmi dari Digital River. _So, now you're on your own_...  
2. Install paket `wimtools`.  
`wimtools` adalah perkakas untuk memanipulasi berkas `WIM` (_Windows Imaging_) yang biasa digunakan dalam _installer_ Windows.  
{% highlight sh %}
aptitude install wimtools
{% endhighlight %}  


**Siapkan Partisi Windows**

Gunakan `gparted` atau _partitioner_ favorit Anda lainnya untuk membuat partisi khusus bagi Windows. Ingat, _backup_!. Bermain dengan partisi adalah urusan serius. Selalu cadangkan data penting sebelum menyunting partisi.
Berikut urutan yang mesti dilakukan:  

- Siapkan (buat) partisi  
- Format partisi baru ini ke NTFS  
- Normalnya, installasi Windows menyaratkan dipasang pada partisi aktif agar Windows dapat memasang _bootloader/boot manager_ dengan benar. Jadi, beri partisi NTFS ini tanda (_flag_) `boot`.  
Sebenarnya dalam kasus kita tidak akan berpengaruh banyak, karena kita akan menggunakan GRUB2 sebagai _bootloader_.  

**Install Windows**

- Buat titik kait untuk bekerja sementara dengan ISO dan partisi Windows.  
{% highlight sh %}
mkdir /tmp/{iso,hdd}
{% endhighlight %}  
- Kaitkan berkas ISO Windows 7.  
{% highlight sh %}
mount -o loop windows_iso /tmp/iso
{% endhighlight %}  
- Terdapat banyak edisi Windows dalam sebuah berkas ISO, ketahui _index_ edisi Windows dengan perintah:  
{% highlight sh %}
wimlib-imagex info /tmp/iso/sources/install.wim
{% endhighlight %}  
- Misal jika kita hendak memasang edisi _Ultimate_ yang ada di indeks ke-5 ke partisi `/dev/sda2`:  
{% highlight sh %}
wimlib-imagex apply /tmp/iso/sources/install.wim 5 /dev/sda2
{% endhighlight %}  
-  Kaitkan partisi Windows untuk mempersiapkan berkas yang dibutuhkan partisi tersebut agar bisa _boot_.  
{% highlight sh %}
mount /dev/sda2 /tmp/hdd
mkdir /tmp/hdd/sources
cp -r /tmp/hdd/Windows/Boot/PCAT /tmp/hdd/Boot
cp /tmp/hdd/Boot/bootmgr /tmp/hdd
{% endhighlight %}  
Sayangnya belum ada aplikasi Linux yang mampu menyunting berkas `BCD`, karenanya, agar partisi Windows ini mampu _booting_ sementara kita pakai `BCD` dan berkas _recovery_ (`winRE.wim`) dari berkas ISO agar nanti partisi _boot_ ke _Windows Recovery Environment_ dan kita memperbaiki `BCD` dari sana.  
{% highlight sh %}
cp /tmp/hdd/Windows/Boot/DVD/PCAT/{boot.sdi,BCD} /tmp/hdd/Boot
cp /tmp/hdd/Windows/System32/Recovery/winRE.wim /tmp/hdd/sources/boot.wim
{% endhighlight %}

**Langkah Tambahan, _unattendend install_**

Anda bisa melewati langkah ini dan langsung menuju langkah _Finishing_ jika hendak memasang Windows secara normal.  
Sesuai namanya, _unattendend install_ berarti proses pemasangan Windows yang tidak memerlukan campur tangan manusia. Proses pemasangan berjalan otomatis dengan cara kita menyediakan berkas jawaban bagi _installer_ sehingga tidak diperlukan memasukkan jawaban secara manual.

Rujuk laman [technet.microsoft.com](http://technet.microsoft.com/en-us/library/cc766245%28v=ws.10%29.aspx) untuk memahami bagaimana membuat berkas jawaban secara mandiri, atau bisa juga menggunakan layanan dari [windowsafg.no-ip.org](http://windowsafg.no-ip.org/win7x86_x64.html).

Salin berkas jawaban yang telah dibuat:

{% highlight sh %}
cp Unattend.xml  /tmp/hdd/Windows/System32/sysprep
{% endhighlight %}  

Berkas `Unattend.xml` dapat berisi data yang penting, karenanya baik kita buat _script_ untuk menghapusnya jika pemasangan Windows telah usai.  
Buat sebuah _script_, misalnya `SetupComplete.cmd` untuk menghaapus berkas yang tidak lagi diperlukan, bahkan untuk menerapkan _registry tweaking_.
{% highlight sh %}
mkdir /tmp/hdd/Windows/Setup/Scripts
cp SetupComplete.cmd /tmp/hdd/Windows/Setup/Scripts
{% endhighlight %}  

**_Finishing_**

Proses pemasangan Windows telah selesai. Lepaskan partisi dan berkas ISO Windows dari kaitannya.  
Masukkan partisi Windows 7 ke dalam GRUB:
{% highlight sh %}
sudo update-grub
{% endhighlight %}  

**_Reboot_**

Hidupkan ulang komputer.  
Komputer akan _restart_ beberapa kali:

- _restart_ pertama komputer akan _boot_ ke _Windows Recovery Environment_ dan akan mencoba memperbaiki `BCD`.  
- _boot_ kedua Windows akan mulai installasi tahap pertama  
- _boot_ ketiga Windows akan mulai installasi tahap akhir

**Catatan**

GNU/Linux tidak menulis data secara rapat berimpitan (_contiguous_) sebagaimana Windows. Karenanya setelah Anda mengikuti tutorial ini dan berhasil masuk Windows, langkah pertama yang harus dilakukan adalah _defrag_ Windows.

**Lampiran**

- Contoh berkas [Unattend.xml](https://drive.google.com/file/d/0B30UcQryc941UlpBaVZQUGhIcjg/view?usp=sharing)
- Contoh berkas [SetupComplete.cmd](https://drive.google.com/file/d/0B30UcQryc941Mk1BVmVEUmNDS00/view?usp=sharing)




