---
layout: post
title: Update Berkas ISO Linux Menggunakan zsync
tags: [iso, zsync, linux]
comments: true
---

_I love collecting Linux ISO files..._  
Menyimpan berkas ISO Linux merupakan kebiasaan lama saya yang didasari alasan; (1) susahnya koneksi internet -- jadi sayang jika ISO hasil unduhan dihapus begitu saja, (2) sebagai sarana _backup and recovery_ sekiranya komputer mengalami masalah dan (3) karena saya suka menjajal atau berpindah distro.  
Kini saya tidak lagi gemar berpindah distro, Debian Sid telah menyediakan apa yang saya butuhkan. Namun, jika memungkinkan, saya masih tetap mengunduh satu-dua berkas ISO Linux sekedar untuk memenuhi rasa penasaran.

Satu masalah dengan mengkoleksi berkas ISO Linux adalah ia cepat menjadi kuno (_out of date_). Mayoritas distro Linux mengeluarkan rilis terbaru tiap 6-12 bulan, bandingkan dengan Microsoft Windows yang merilis versi anyar tiap 2-5 tahun.  
Tidak lama berselang, ISO yang kita unduh dengan susah payah akhirnya hanya akan menyesaki HDD/SSD karena di internet telah beredar versi baru dengan kernel baru, fitur baru dan perbaikan-perbaikan pada _bugs_. Dan saya pun mengunduh ISO versi terbaru, demikian siklus pun berulang.  

Untungnya, beberapa distro seperti Debian, Ubuntu dan [SystemRescueCD](http://www.system-rescue-cd.org/Beta-x86), menyediakan berkas `.zsync` yang memungkinkan penggunanya untuk hanya mengunduh selisih (_delta_) berkas ISO untuk _upgrade_ dari ISO versi sebelumnya.

Lalu, apa itu `zsync`? Dikutip dari [situsnya](http://zsync.moria.org.uk/); _"zsync is a file transfer program. It allows you to download a file from a remote server, where you have a copy of an older version of the file on your computer already. zsync downloads only the new parts of the file. It uses the same algorithm as [rsync](http://rsync.samba.org/). However, where rsync is designed for synchronising data from one computer to another within an organisation, zsync is designed for file distribution, with one file on a server to be distributed to thousands of downloaders. zsync requires no special server software "just a web server to host the files" and imposes no extra load on the server, making it ideal for large scale file distribution."_

Berikut yang saya lakukan untuk memperbaharui berkas `ubuntu-16.04-desktop-amd64.iso` yang saya unduh sekitar bulan Mei menjadi `ubuntu-16.04.1-desktop-amd64.iso` yang rilis Agustus ini:

<pre>
zsync -i ubuntu-16.04-desktop-amd64.iso http://kambing.ui.ac.id/iso/ubuntu/releases/16.04.1/ubuntu-16.04.1-desktop-amd64.iso.zsync  
</pre>

Seperti terlihat pada _log_ di bawah ini, berkas `ubuntu-16.04-desktop-amd64.iso` dan `ubuntu-16.04.1-desktop-amd64.iso` memiliki 52,6% isi yang sama, atau sekitar 47% pembaharuan. Jadi, alih-alih mesti mengunduh 1,5GB berkas `ubuntu-16.04.1-desktop-amd64.iso`, dengan bantuan `zsync` saya hanya perlu mengunduh sekitar 700MB untuk memperbaharui `ubuntu-16.04-desktop-amd64.iso` menjadi `ubuntu-16.04-desktop-amd64.iso`.

<pre>
zsync -i ubuntu-16.04-desktop-amd64.iso http://kambing.ui.ac.id/iso/ubuntu/releases/16.04.1/ubuntu-16.04.1-desktop-amd64.iso.zsync
#################### 100.0% 755.4 kBps DONE

reading seed file ubuntu-16.04-desktop-amd64.iso: ******************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
********************************************************************************
******************************Read ubuntu-16.04-desktop-amd64.iso. Target 52.6% complete.
downloading from http://kambing.ui.ac.id/iso/ubuntu/releases/16.04.1/ubuntu-16.04.1-desktop-amd64.iso:
##########---------- 52.6% 8.7 kBps
#################### 100.0% 1013.1 kBps DONE

verifying download...checksum matches OK
used 796721152 local, fetched 717338852
</pre>