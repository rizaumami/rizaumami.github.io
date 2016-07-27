---
layout: post
title: Update Irit Bandwidth Menggunakan debdelta
tags: []
comments: true
---

Menggunakan Debian Sid sebagai sistem operasi sehari-hari lumayan menguras kuota langganan internet. Maklumlah, sebagai _rolling release_, pembaharuan untuk Sid hampir dipastikan bergulir tiap saat, ditambah saya termasuk orang selalu ingin mencoba versi _software_ terbaru, jadi _update_ tiap hari adalah hampir menjadi keharusan.  
Untunglah Debian memiliki `debdelta`. 

Dari [debdelta.debian.net](http://debdelta.debian.net/): _"_`debdelta` _adalah perangkat lunak yang dirancang untuk memperhitungkan perubahan di antara paket Debian. Perubahan tersebut (yang kita sebut sebagai_ delta _) serupa dengan keluaran program_ `diff` _dalam arti_ delta _tersebut dapat digunakan untuk menyimpan dan mengirimkan hanya perubahan di antara paket Debian."_  
Gambaran sederhananya misal seperti berikut; `paket-a-1.deb` berukuran 2MB yang terpasang pada sistem ternyata mendapat pembaharuan `paket-a-2.deb` yang berukuran 3,5MB. Pada sistem normal, `APT` akan mengunduh `paket-a-2.deb` yang berukuran 3,5MB tersebut untuk kemudian dipasang pada sistem. Namun dengan menggunakan `debdelta`, sistem hanya akan mengunduh berkas delta `paket-a-1_2.delta` yang hanya sebesar selisih `paket-a-1.deb` dan `paket-a-2.deb`, yakni dalam kisaran 1,5MB.

Menarik bukan? Namun tentu saja tidak ada obat yang dapat mengobati segala macam penyakit. `debdelta` bagus untuk menghemat _bandwidth_ namun ia boros akan sumber daya komputer, karena komputer perlu untuk melakukan _patching_ agar berkas delta menghasilkan berkas `deb` yang dapat dipasang di sistem.  
Jadi kesimpulan sederhananya; `debdelta` bagus bagi kita yang terkendala sambungan internet, baik karena mahal atau pun lambat. Bagi yang sambungan internetnya cepat dan murah, `debdelta` hampir tidak memberikan banyak arti.

Jika sudah mantap hati untuk menggunakan `debdelta`, silahkan ikuti langkah berikut:  

- Pasang paket `debdelta`
{% highlight sh %}
sudo apt install debdelta
{% endhighlight %}  
- Paket `debdelta` akan memasang tiga berkas pada sistem; (1) `debdelta)`, (2) `debdeltas`, dan (3) `debdelta-upgrade`. `debdelta-upgrade`-lah yang bertanggung jawab melakukan _upgrade_ menggunakan delta.  
`debdelta-upgrade` merupakan perintah yang terpisah dari `APT`, jadi baiknya kita buat alias agar proses pemutakhiran sistem lebih ringkas dan nyaman.
{% highlight sh %}
alias deltaupgrade='sudo apt update && sudo debdelta-upgrade -v && sudo apt full-upgrade'
{% endhighlight %}  

Kita telah berhasil memasang `debdelta` dan menentukan alias bagi penggunaan `debdelta-upgrade`. Selanjutnya jika hendak memutakhirkan sistem, maka kita cukup mengetikkan perintah `deltaupgrade` di terminal.

_Happy deltaupgrade!_
