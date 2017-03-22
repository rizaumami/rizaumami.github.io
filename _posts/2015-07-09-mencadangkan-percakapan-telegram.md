---
layout: post
title: Backup Telegram Chat
tags: [telegram, backup]
comments: true
---

Dibalik banyak keunggulan [Telegram](https://telegram.org/) dibanding aplikasi *chat* lain, ia memiliki kelemahan mendasar; tidak memiliki fitur *backup*.
Mungkin mereka beralasan karena Telegram *cloud based*, jadi pesan akan dapat diakses kapan saja, dari mana saja, menggunakan aplikasi apa saja, dan menggunakan perangkat apa saja.

*Well, backup chat* adalah fitur mendasar aplikasi percakapan, setiap orang akan memiliki masing-masing alasan untuk memiliki catatan (*log*) percakapan. Dan sambil menunggu semoga fitur ini diterapkan oleh pihak Telegram, berikut cara untuk mencadangkan percakapan Telegram Anda.

* Pasang [telegram-cli](https://github.com/vysheng/tg).
* Jika Anda memasang `telegram-cli` menggunakan `git clone`, masuk ke map `tg` menggunakan perintah `cd tg`.
* Pasang `netcat-traditional` menggunakan perintah 

  ```sh
  sudo aptitude install netcat-traditional
  ```

* Jalankan perintah berikut:

  ```sh
  bin/telegram-cli -vvvvRC -k tg-server.pub -WdP 1337 &
  ```

  Perintah tersebut akan menjalankan `telegram-cli` sebagai *daemon* dan membuka UDP *socket* `1337`.

* Untuk mencadangkan pesan, jalankan perintah berikut:

  ```sh
  echo -e 'history PegeLinux 100\n' | nc.traditional -w 1 127.0.0.1 1337 >> $(date +%F_%R)_tele.log  
  ```

  Perintah ini akan mengambil riwayat (`history`) dari *peer* `PegeLinux` sebanyak seratus pesan (`100`). Ganti *peer* dengan *username* atau grup telegram, jika *username* atau nama grup mengandung spasi, ganti spasi dengan garis bawah (*underscore*, `_`). Banyak pesan yang dapat diambil, selama percobaan saya beragam, kadang bisa 100, 300, bahkan 500 pesan. Nampaknya, banyak pesan tergantung keaktifan percakapan Anda dengan *user* atau grup.

* Untuk mematikan proses `telegram-cli`, jika Anda lupa mencatat `PID` pada langkah sebelumnya, gunakan perintah berikut:

  ```sh
  ps aux | grep telegram-cli
  ```

  Misal hasilnya sebagai berikut:

  ```sh
  iza      28367  0.0  0.0 264744 12300 pts/0    S    16:26   0:00 bin/telegram-cli -vvvvRC -k tg-server.pub -WdP 1337  
  iza      28672  0.0  0.0  12728  2204 pts/2    S+   17:05   0:00 grep --color=auto telegram-cli
  ```

* Matikan proses menggunakan perintah:

  ```sh
  kill -9 28367
  ```
