---
layout: post
title: Ninite, Install Software Windows Tanpa Rumit
tags: [ninite, windows]
comments: true
---

_Windows package management is suck!_  
Dalam GNU/Linux paket tersedia dalam _repository_ terpusat dan untuk memasangnya tersedia _package manager_ atau penata paket yang mumpuni, sehingga pengguna tinggal _install and forget_. Dalam Windows kita mesti mengunduh piranti lunak dari berbagai laman yang tersebar di internet, memasangnya dengan seksama agar tidak turut memasang aplikasi "pembonceng", juga _update_ dan segala tetek bengek lainnya mesti diperhatikan manual oleh si pengguna.

Izinkan saya untuk sedikit mengabarkan berita gembira. Dalam Windows, kita dapat menggunakan piranti lunak serupa penata paket dalam Linux, yakni [`Ninite`](https://ninite.com/) dan [`Chocolatey`](https://chocolatey.org/).  
Artikel ini hanya akan membahas [`Ninite`](https://ninite.com/) saja. [`Ninite`](https://ninite.com/) memang tidak memiliki fitur selengkap penata paket dalam Linux, namun tetap akan sangat membantu dibandingkan mesti satu-per-satu memasang piranti lunak yang kita butuhkan. 

Berikut langkah menggunakan [`Ninite`](https://ninite.com/):
1. Kunjungi laman <https://ninite.com/>
2. Centang perangkat lunak yang ingin dipasang

   [![Ninite]({{ site.baseurl }}/assets/img/ninite.png)]({{ site.baseurl }}/assets/img/ninite.png)
   
3. Tekan tombol **Get Installer** untuk mengunduh berkas `ninite`. Berkas `ninite` ini berupa berkas dengan nama aplikasi-aplikasi yang kita pilih pada langkah 2, misalnya `Ninite 7Zip Essentials Firefox Foxit Reader GIMP Installer.exe`.
4. Jalankan berkas `ninite` untuk memasang aplikasi.

   [![ninite_install]({{ site.baseurl }}/assets/img/ninite_install.png)]({{ site.baseurl }}/assets/img/ninite_install.png)
   
5. Profit!

Simpan berkas `ninite` dari langkah 3, agar jika sewaktu-waktu perlu untuk memutakhirkan aplikasi, kita tinggal menjalankan ulang berkas `ninite` tersebut dan ia akan memutakhirkan piranti lunak yang memiliki versi lebih anyar.

_Not bad eh..._
