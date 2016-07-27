---
layout: post
title: Script Pengambil Top Thread Kaskus dan Subreddit r/linux
tags: [script, bash, awk, kaskus, reddit]
comments: true
---

*Script* ini awalnya diperuntukkan menyediakan tautan bacaan di grup Telegram PegeLinux. Namun seiring dibukanya API *bot* Telegram, *script* ini berkurang gunanya karena anggota grup dapat langsung meminta ke *bot*.

{% highlight sh %}

#!/usr/bin/env bash

# script pengambil laman top thread Kaskus dan subreddit r/linux
#
# v0.3

# VARS -------------------------------------------------------------------------

temp_dir=$(mktemp -d)

# FUNCTIONS --------------------------------------------------------------------

get_linuxsubreddit() {
wget http://www.reddit.com/r/linux/.json -O $temp_dir/rlinux

awk '{gsub(/t3_|"[,] "created_utc/, "\n")};1' $temp_dir/rlinux | \
awk -F '"' '((NR<22)&&($5=="url")){print "[redd.it/"$1"]",$13$14$15}' >> $temp_dir/koran
}

get_kaskushotthread() {
wget http://m.kaskus.co.id/ -O $temp_dir/kaskus_ht
awk '{gsub(/<a href="|<\/a>/,"\n")}; 1'  $temp_dir/kaskus_ht | \
awk -F'/[?]ref=forumlanding&med=hot_thread">' -v OFS='\n' '/hot_thread/{print $2,"m.kaskus.co.id"$1}' >> $temp_dir/koran
}


# MAIN -------------------------------------------------------------------------

printf "#koranpagi $(date +%Y.%M.%d)\n\nhttp://www.reddit.com/r/linux\n" > $temp_dir/koran
get_linuxsubreddit

printf "\nKaskus Hot Threads\n\n" >> $temp_dir/koran
get_kaskushotthread

xclip -sel clip $temp_dir/koran

printf '\nKasKus Hot Threads telah disalin ke dalam clipboard\n'

rm -r $temp_dir

{% endhighlight %}

### How it works

Fungsi `get_linuxsubreddit` bekerja sebagai berikut; wget akan mengambil laman subreddit `http://www.reddit.com/r/linux/.json` dan menyimpannya sebagai berkas `$temp_dir/rlinux`.
Awk kemudian akan mengganti semua *string* `t3_` dan `created_utc/, ` dengan baris baru (*newline*), hasilnya akan disaring kembali dimana hanya 22 baris teratas (`NR<22`) dan baris yang kolom ke-5-nya mengandung *string* url (`$5=="url"`) yang akan diproses, hasil akhirnya, kolom 1, 13, 14 dan 15 `{print "[redd.it/"$1"]",$13$14$15}` akan disisipkan ke dalam berkas `$temp_dir/koran`.

Fungsi `get_kaskushotthread` bekerja sebagai berikut; wget akan mengambil laman `http://m.kaskus.co.id/` dan mentimpannya sebagai berkas `$temp_dir/kaskus_ht`.
Awk akan mengganti semua *tag* HTML dengan spasi (`{gsub(/<a href="|<\/a>/,"\n")}; 1`), hasilnya akan disaring kembali dimana hanya baris yang mengandung *string* `ref=forumlanding&med=hot_thread` yang akan diproses, hasil akhirnya kolom 1 akan ditempatkan setelah kolom 2 (`print $2,"m.kaskus.co.id"$1`) dan disisipkan ke dalam berkas `$temp_dir/koran`.

Untuk memudahkan pemostingan ke Telegram, daftar thread disalin oleh `xclip` ke *clipboard* dan siap diposkan (`xclip -sel clip $temp_dir/koran`).
