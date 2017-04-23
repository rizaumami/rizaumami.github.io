---
layout: post
title: Kisah Memasang Solus yang Tidak Berakhir Mulus 
tags: [solus]
comments: true
---

Sedikit bosan dengan Gnome shell yang telah sekian tahun dipakai, iseng melirik [Solus project](https://solus-project.com) yang lumayan gencar dipromosikan di [r/linux](https://reddit.com/r/linux). Saya mengenal Solus sejak zaman ia mengambil dasar Debian dan Gnome 2, _I was quite like it_ dan kini ingin mencoba sejauh mana perkembangannya.

Meski [Budgie](https://solus-project.com/budgie), DE andalan Solus bisa dipasang dengan mudah di Debian Sid, namun saya lebih memilih untuk memasang distro Solus secara menyeluruh, mengingat sebuah distro tidak sekedar tampilan saja melainkan keterpaduan semua lini pendukungnya.

Berbekal sisa kuota kalong, akhirnya berhasil juga [_torrenting_ berkas ISO Solus](http://linuxtracker.org/?page=torrent-details&id=35532f8160529469960457de58892bc1d198c731) yang mencapai 1,2GB. Tinggal mencari _flash disk_ sebagai sarana pemasangan. _Well, here's the trouble begin_, saya tidak punya _flash disk_ (UFD) tidak terpakai. Umumnya tutor membuat _installer_ mengharuskan menggunakan `dd` yang akan menghapus seluruh isi UFD, _which is a big no no_. Apa daya? Hmmm, kita coba _boot_ ISO langsung dari HDD menggunakan GRUB 2.

GRUB 2 (selanjutnya hanya akan ditulis GRUB saja) bisa _booting_ beberapa jenis berkas ISO Linux langsung dari disk. Selama ini saya biasa _booting_ berkas ISO dari distro-distro berbasis Ubuntu, namun karena Solus ini bukan turunan Ubuntu, sepertinya akan berbeda dari biasanya. Agar GRUB bisa menjalankan ISO langsung, maka ia harus bisa mengenali di mana kernel dan initram berkas ISO tersebut. Karenanya kita mesti membedah isi berkas ISO-nya. Tidak perlu _extract_, cukup _mounting_ saja. Kebetulan di Gnome sangat mudah untuk _mounting_ berkas ISO, cukup klik ganda berkas ISO-nya maka ia akan _mounted_ di `/dev/loop`.  
Benar saja, susunan _installer_ Solus lebih menyerupai _installer_ Fedora dibanding Ubuntu. Jadi saya pun _searching_ bagaimana entri _boot_ ISO Fedora untuk GRUB dan memberikan penyesuain seperlunya. Berikut entri yang harus dimasukkan ke GRUB. Karena hanya untuk sementara, saya masukkan langsung ke `/boot/grub/grub.cfg`.

```bash
menuentry 'SolusOS 2017.04.18 Budgie' {
  set isofile='/home/iza/Software/ISO/Solus-2017.04.18.0-Budgie.iso'
  loopback loop $isofile
  linux (loop)/boot/kernel boot=casper iso-scan/filename=$isofile noprompt noeject root=live:CDLABEL=SolusLiveBudgie ro rd.live.image rd.luks=0 rd.md=0 rd.dm=0 -- 
  initrd (loop)/boot/initrd.img
}
``` 

Parameter kernel saya dapatkan dari berkas `isolinux/isolinux.cfg` dalam _mounted Solus ISO_.

Akhirnya, laptop pun _booted_ dan bisa masuk desktop _live_ Solus dengan lancar. Setelah sedikit mencoba-coba, akhirnya bulat tekad untuk memasangnya langsung ke SSD. _But, wait_. Proses pemasangan terhenti karena ternyata partisi yang disediakan tidak mencukupi. Solus membutuhkan setidaknya partisi sebesar 10GB.  
Baiklah, tidak masalah. Saya pun membuka `gparted` dan mengatur partisi seperlunya agar memuaskan kebutuhan _installer_. Setelah selesai geser kiri - geser kanan, ternyata `gparted` menolak untuk menerapkan susunan partisi yang baru diatur ini...  
Telusur punya telusur, sepertinya `gparted` menolak menerapkan pengaturan partisi baru karena Solus _live_ ini _booted_ langsung dari SSD yang hendak dirubah susunan partisinya. Hmmm, masuk akal. Bagaimana sekarang? Kembali teringat jika _boot_ dari distro berbasis Ubuntu, mereka bisa _boot_ langsung _boot_ dari `ramdisk`. Sepertinya kali ini pun mesti mencoba untuk _booting_ Solus langsung dari `ramdisk`, jadi `gparted` tidak akan mengeluh bahwa diska sedang dipakai. Sekilas _searching_ di internet, akhirnya menemukan laman [Fedora: Booting Fedora LIVE to RAM](http://blog.parahard.com/2013/10/fedora-booting-fedora-live-to-ram.html). Mari dicoba.

Laptop pun _rebooted_. Pada menu GRUB, tekan tombol `E` pada _keyboard_ untuk melakukan penyuntingan pada entri _boot_ Solus, rubah `rd.live.image` menjadi `rd.live.ram` pada baris parameter kernel. Setelah selesai, tekan `CTRL+X` untuk _booting_ entri tersebut.

Kembali masuk ke desktop _live_ Solus, langsung mengulang penyuntingan partisi menggunakan `gparted`, namun tak dinyana, masih tetap tidak bisa. Pada ISO Ubuntu dan turunannya, ini bisa diakali dengan melepaskan kaitan (_unmount_) partisi tempat berkas ISO berdiam, namun hal ini tidak berlaku pada ISO Solus. Hmmm, sepertinya memang harus _boot_ dari diska selain yang hendak diatur.

_I give up_. Tangan pun mengais-ais serakan barang di laci, mencari di mana HDD _external_ berada. Jika kali ini pun gagal, putus sudah rencana untuk memasang Solus.   
HDD _external_ pun ditemukan. Setelah kembali mengulang langkah sebagaimana diceritakan di awal, akhirnya pemasangan Solus pun berjalan dengan lancar. Mari kita _reboot_ dan masuk ke desktop baru ini.

_It's quite a fresh environment..._  
Semua berjalan gegas, piranti lunak pun terpasang pas, tidak kurang, tidak juga berlebihan.  
_But, then again, it's like something blocking my way to enjoy this distro...._ Kini masalahnya adalah desktop baru ini tidak bisa mengenali WWAN GOBI 3000 yang terpasang dalam laptop. Padahal WWAN ini menjadi andalan koneksi internet. _Tethering_ wi-fi dari telepon seluler berjalan lancar, namun bukanlah pilihan, hanya untuk kala darurat.

Kembali mencari jawaban di dunia maya. Menemukan sebuah trik sederhana; hidupkan layanan `ModemManager`, mungkin belum berjalan. _Let me see..._

```bash
sudo systemctl enable ModemManager
sudo systemctl start ModemManager
``` 

Aahhh... Akhirnya WWAN bisa dikenali.  
Dengan semangat membuat profil Indosat, menjajal untuk menghubungi internet. _But, no shit Sherlock..._, tidak bisa terhubung ke internet! Notifikasi di _applet_ hanya berputar, berputar, dan berputar...

Hhhh... _I'm tired of this shit, I'm going back to Debian Sid. It's boring, but it's working..._    
 
Sekian sepenggal kisah yang tidak berakhir gembira...


