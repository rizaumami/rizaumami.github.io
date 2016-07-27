---
layout: post
title: Backup Telegram Chat - Bagian 2
tags: [telegram, backup]
comments: true
---

Lihat artikel *backup* Telegram *chat* sebelumnya di [Backup Telegram Chat](http://rizaumami.github.io/2015/07/09/mencadangkan-percakapan-telegram/).

Mencadangkan percakapan Telegram pada artikel di atas tingkat keberhasilannya beragam, terutama jika kita meminta jumlah *history* yang besar. Jumlah terbesar *histroy* yang berhasil saya minta adalah sekitar 950 *post*, padahal percakapan pada grup Telegram yang saya ikuti seringkali melebih 1000 pos per hari.

Cara berikut adalah sama dengan cara di artikel yang lalu; yakni dengan menjadikan `telegram-cli` sebagai `daemon` namun dengan menuliskan `log`. Jadi kita tidak akan berinteraksi dengan `telegram-cli` ini, melainkan membiarkannya merekam semua percakapan dari akun Telegram kita.

*So, here is how...*

* Pasang `telegram-cli`.

* Jalankan sebagai `daemon`.
{% highlight sh %}
telegram-cli -vvvvRCf -k tg-server.pub -L ~/Documents/telegram.log -WdP 1337 &
{% endhighlight %}

Perintah tersebut akan menjalankan `telegram-cli` sebagai *daemon* dan membuka soket UDP `1337` dan menyimpan rekaman percakapan di `~/Documents/telegram.log`.

* Berkas `log` adalah berisi semua percakapan pada akun Telegram serta `stdout` dari `telegram-cli`, jadi tidak teratur dan terkelompokkan.

Untuk mendapatkan `log` yang rapi, kita harus menyaringnya. Misal menggunakan `awk` seperti perintah berikut:

{% highlight sh %}
awk '!/ is typing in chat | [(]was online | marked read | is uploading |\x0|^got SIGHUP.|^ \*\*../' ~/Documents/telegram.log | awk '/^\[..:..\]/ { p = $2 == "PegeLinux" } p' | awk -F'»»»|>>>' '{gsub(/]  PegeLinux /,"] ")}{printf "%-30s%s\n", $1,$2}' > pegelinux-$(date +%y%m%d-%H.%M).log
{% endhighlight %}

*Well, what a shitty command, but it's work ...*

Perintah `awk` pertama akan membuang baris dengan *string* `is typing in chat`, `(was online`, `marked read `, `is uploading`, `got SIGHUP`, dan `***`.

Perintah `awk` kedua akan menampilkan semua baris yang mengandung kata **PegeLinux** juga baris di bawahnya hingga akhirnya menemukan kembali baris dengan kata **PegeLinux** di dalamnya.

Perintah `awk` ketiga akan mengganti semua *string* `]  PegeLinux ` dengan `]` dan menggeser kolom kedua 30 karakter ke kanan.

Ketiga `awk` tersebut akan menghasilkan berkas `log` yang lebih rapi di pegelinux-$(date +%y%m%d-%H.%M).log, yang namanya berubah sesuai waktu perintah dijalankan.

* Untuk mematikan proses `telegram-cli`, jika Anda lupa mencatat `PID` pada langkah sebelumnya, gunakan perintah berikut:

{% highlight sh %}
ps aux | grep telegram-cli
{% endhighlight %}

Misal hasilnya sebagai berikut:

{% highlight sh %}
iza      28367  0.0  0.0 264744 12300 pts/0    S    16:26   0:00 bin/telegram-cli -vvvvRC -k tg-server.pub -WdP 1337
iza      28672  0.0  0.0  12728  2204 pts/2    S+   17:05   0:00 grep --color=auto telegram-cli
{% endhighlight %}

Matikan proses menggunakan perintah:

{% highlight sh %}
kill -9 28367
{% endhighlight %}
