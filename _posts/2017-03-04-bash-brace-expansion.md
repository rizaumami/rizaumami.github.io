---
layout: post
title: Bash Brace Expansion
tags: [bash]
comments: true
---

Hmmm, apakah gerangan `bash` _brace expansion_ itu?  
`Bash` _brace expansion_ adalah sebuah cara untuk menghasilkan _string_ berdasar pola tertentu.   

_I don't get it..._  
Izinkan saya untuk menjelaskan melalui contoh sebuah _script_ `bash`.  
Silahkan buka [GitHub gist TeleBSE](https://gist.github.com/rizaumami/05ab15ceaba910974ad416d10ed4e4bf), sebuah _script_ `bash` untuk mengambil tautan ke berkas PDF Buku Sekolah Elektronik. Dalam _script_ tersebut ada beberapa baris yang mendayagunakan mekanisme `bash` _brace expansion_, mari kita bahas satu per satu.

Yang pertama adalah:

{% highlight sh %}
mkdir -p $BASEDIR/{SD_{1..6},SMP_{7..9},SMA_{10..12},SMK_{10..12}}  
{% endhighlight %}

Arti dari baris tersebut adalah: Buat map-map (_folders_) `SD_1`, `SD_2`, `SD_3`, `SD_4`, `SD_5`, `SD_6`, `SMP_7`, `SMP_8`, `SMP_9`, `SMA_10`, `SMA_11`, `SMA_12`, `SMK_10`, `SMK_11`, dan `SMK12`.  
Jika tanpa `bash` _brace expansion_, maka kemungkinan perintahnya adalah sebagai berikut:

{% highlight sh %}
cd $BASEDIR
mkdir SD_1 SD_2 SD_3 SD_4 SD_5 SD_6 SMP_7 SMP_8 SMP_9 SMA_10 SMA_11 SMA_12 SMK_10 SMK_11 SMK12  
{% endhighlight %}

Wow! _Is't it amazing..._  
Jadi, `bash` _brace expansion_ menghasilkan string baru dengan pola tertentu. Dari contoh di atas:

{% highlight sh %}
SD_{1..6}  
{% endhighlight %}

Dikembangkan menjadi:

{% highlight sh %}
SD_1 SD_2 SD_3 SD_4 SD_5 SD_6  
{% endhighlight %}

Mari kita lihat contoh berikutnya pada baris:

{% highlight sh %}
for kelas in {SD_{1..6},SMP_{7..9},SMA_{10..12},SMK_{10..12}}; do  
{% endhighlight %}

Baris _for-loop_ ini akan menelusuri tiap map mulai dari `SD_1` hingga `SMK_12`.  
Jika menggunakan _for-loop_ biasa, perintahnya adalah sebagai berikut:

{% highlight sh %}
for kelas in SD_1 SD_2 SD_3 SD_4 SD_5 SD_6 SMP_7 SMP_8 SMP_9 SMA_10 SMA_11 SMA_12 SMK_10 SMK_11 SMK12; do  
{% endhighlight %}

Lihat bagaimana `bash` _brace expansion_ bisa menyederhanakan _script_ kita.  

Dari dua contoh di atas kiranya kita dapat memahami kegunaan `bash` _brace expansion_. Misal, untuk menyingkat dan menyederhanakan _script_ dan mengurangi gerak jari dalam mengetik baris perintah.  

Misal, kita hendak merubah nama atau memindahkan suatu berkas yang memiliki _path_ yang panjang:

{% highlight sh %}
mv /tmp/test/folder/untuk/menunjukkan/path/yang/panjang/dan/dalam/berkas /tmp/test/folder/untuk/menunjukkan/path/yang/panjang/dan/dalam/file  
{% endhighlight %}

Lumayan panjang kan? Meski menggunakan `bash-completion` pun akan tetap membosankan. Di sini kita bisa menggunakan bantuan `bash` _brace expansion_:

{% highlight sh %}
mv /tmp/test/folder/untuk/menunjukkan/path/yang/panjang/dan/dalam/{berkas,file}  
{% endhighlight %}

Mencadangkan sebuah berkas dengan cara menyalin dan memberi akhiran `-bak` pada namanya?

{% highlight sh %}
cp /tmp/myscript.sh{,-bak}  
{% endhighlight %}

Hendak mengembalikan berkas `myscript.sh-bak` kembali menjadi `myscript.sh`?

{% highlight sh %}
mv /tmp/myscript.sh{-bak,}  
{% endhighlight %}

Demikian artikel hari ini, semoga bermanfaat...
