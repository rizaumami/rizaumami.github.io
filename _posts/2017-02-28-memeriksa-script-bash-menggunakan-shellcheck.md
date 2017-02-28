---
layout: post
title: Memeriksa Script Bash Menggunakan Shellcheck
tags: [bash, script, shellcheck]
comments: true
---

Pada masa awal-awal menggunakan Linux dan berhasil menulis `bash` _script_, _I felt like a hacker_.  
Menyadari bahwa bisa menulis beberapa baris untuk mengerjakan tugas tertentu telah membuka cakrawala pikiran saya yang kala itu masih menggunakan Windows. _It's different, simple, but powerfull_.

Dari sana, kemudian banyak membaca dan menulis _script_ bash. Ternyata tidak sesederhana yang semula dipikirkan, namun tetap saja menyenangkan.  
Dari sumber-sumber bacaan, akhirnya saya mengenal yang namanya _good practice_ dalam _programming_. _Well, there are peoples who laugh at your face for saying bash scripting as programming_. Abaikan saja, dan buat `bash` _script_ yang sesuai dengan kaidah yang baik.

Jadi, bagaimanakah rupa `bash` _script_ yang baik? Sejujurnya saya tidak bisa menilai sebuah apakah sebuah `bash` _script_ itu baik atau tidak, semakin banyak yang saya baca mengenai `bash`, semakin banyak yang tidak dimengerti.  
Untungnya, --maklum, sebagai orang Indonesia kita selalu beruntung-- ada alat untuk membantu kita menilai apakah sebuah _script_ `bash` itu baik atau tidak. Nama alat tersebut adalah `shellcheck`.

[`shellcheck`](http://www.shellcheck.net/) adalah piranti lunak untuk menelaah _shell script_ (_static analysis tool for shell scripts_).  
Kita bisa memeriksakan _script_ secara daring di [http://www.shellcheck.net](http://www.shellcheck.net/) atau dengan memasang paket `shellcheck` di distro kesayangan.

Berikut dicontohkan sebuah kasus sederhana; Saya memiliki sebuah map berisi _clone_ dari _repository_ di github yang dirasa menarik. Isinya tidak terlalu banyak, namun akan cukup membosankan jika mesti memutakhirkannya secara manual satu-per-satu. Jadi saya membuat sebuah _script_ bernama `ghupdate` untuk melakukan `git pull` pada tiap repo tersebut. Berikut _script_-nya:

```sh
#!/bin/bash

GITHUB="$HOME/github"

while read repo; do
  cd "$repo"
  printf "\n Pulling $repo ...\n"
  git pull
done < <(find "$GITHUB" -maxdepth 1 -type d)
```

Sekilas terlihat tidak ada yang aneh dari _script_ ini, dan nyatanya ia pun bekerja dengan baik. Mari kita periksa apa pendapat `shellcheck`:

```sh
shellcheck ghupdate
```

Dan hasilnya...

```sh
In ghupdate line 5:
while read repo; do
      ^-- SC2162: read without -r will mangle backslashes.


In ghupdate line 6:
  cd "$repo"
  ^-- SC2164: Use cd ... || exit in case cd fails.


In ghupdate line 7:
  printf "\n Pulling $repo ...\n"
         ^-- SC2059: Don't use variables in the printf format string. Use printf "..%s.." "$foo".

```

Hmmm, ternyata _script_ `ghupdate` ini tidak sebaik yang dikira.  
`shellcheck` setidaknya menunjukkan tiga kelemahan pada _script_. Sayangnya, jika menggunakan `shellchek` dari repo, kita hanya akan disuguhi hasil secara garis besar. Jika menginginkan rincian mengenai kesalahan dan bagaimana memperbaikinya, mengharuskan kita untuk memeriksanya secara daring di [wiki `shellcheck`](https://github.com/koalaman/shellcheck/wiki/).  
Jadi, rincian kelemahan _script_ `ghupdate` di atas dapat diperiksa di:

- [https://github.com/koalaman/shellcheck/wiki/SC2162](https://github.com/koalaman/shellcheck/wiki/SC2162)
- [https://github.com/koalaman/shellcheck/wiki/SC2164](https://github.com/koalaman/shellcheck/wiki/SC2164)
- [https://github.com/koalaman/shellcheck/wiki/SC2059](https://github.com/koalaman/shellcheck/wiki/SC2059)

Dan akhirnya, berikut _script_ `ghupdate` hasil perbaikan:

```sh
#!/bin/bash

GITHUB="$HOME/github"

while IFS="" read -r repo; do
  cd "$repo" || exit
  printf "\n Pulling %s ...\n" "$repo"
  git pull
done < <(find "$GITHUB" -maxdepth 1 -type d)
```

_Happy shellcecking..._
