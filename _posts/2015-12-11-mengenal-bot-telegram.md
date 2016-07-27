---
layout: post
title: Mengenal Bot Telegram
tags: [telegram,bot]
comments: true
---

[Telegram](https://telegram.org/) adalah sistem perpesanan yang masih belia, jadi wajar jika Anda belum mengenalnya. Ditambah dengan istilah bot tentunya menjadi semakin asing. Karenanya, baik kita awali artikel ini dengan penjabaran istilah Telegram dan bot.

- Telegram adalah sebuah sistem perpesanan yang lintas *platform* dan berpusat pada keamanan dan kerahasiaan pribadi penggunanya.
- `Bot` adalah program komputer yang melakukan pekerjaan tertentu secara otomatis.

Aplikasi perpesanan pastinya telah jamak dalam kehidupan sehari-hari kita, namun mengapa ada *bot* dalam Telegram?
Bot adalah sebuah mesin, dibuat untuk meringankan pekerjaan manusia. Dalam kaitan bot dengan Telegram, bayangkan jika Anda ingin mengirimkan sebuah gambar ke teman bicara dalam Telegram, alih-alih membuka peramban web dan mengetikkan kata kunci di mesin pencari kemudian mengirimkan hasilnya di Telegram, Anda tinggal memanggil bot dan mengetikkan kata kunci semisal: `/img <gambar_yang_dicari>`, maka hasilnya langsung keluar dalam aplikasi Telegram.

Bot pencari gambar hanyalah salah satu kegunaan bot yang sederhana. Bot bisa digunakan untuk mencari di mesin pencari, wiki, youtube, torrent, cuaca, nilai tukar, alih bahasa, pengaturan grup, dan bahkan program yang sangat rumit.

Daya tarik utama Telegram adalah ia dapat dijalankan pada beragam perangkat dan sistem operasi, tidak hanya telepon genggam, namun juga komputer dan perangkat pintar serupa komputer lainnya. Telegram dan bot dapat memudahkan kehidupan keseharian kita tanpa harus terpaku di depan komputer.

Komunikasi utama dengan peladen (*server*) Telegram dilakukan melalui protokol [MTProto](https://core.telegram.org/mtproto), sebuah protokol biner buatan Telegram sendiri. Jadi, jika kita hendak membuat bot Telegram, ia perlu untuk berkomunikasi dengan peladen Telegram menggunakan MTProto, dan kita punya dua pilihan untuk ini; (1) menulis bot secara manual menggunakan bahasa pemrograman yang kita kuasai, dan (2) menggunakan [tg](https://github.com/vysheng/tg/), sebuah *telegram terminal client* untuk sistem Unix.

tg atau biasa disebut telegram-cli, adalah *client* Telegram yang ditulis menggunakan bahasa `C` dan memiliki *library wrapper* untuk [lua](https://en.wikipedia.org/wiki/Lua_(programming_language)) dan [python](https://en.wikipedia.org/wiki/Python_%28programming_language%29). Ini artinya kita dapat menulis program bot menggunakan bahasa lua dan python yang relatif lebih mudah jika dibandingkan harus berhubungan langsung dengan MTProto.

Jadi, pada awal perkembangan dunia bot di Telegram, hampir semua bot dibuat menggunakan telegram-cli dan lua. Bot yang paling terkenal adalah [telegram-bot](https://github.com/yagop/telegram-bot/) buatan Yago Perez.
Bot telegram-cli bekerja layaknya akun pribadi (karena memang ia adalah akun biasa), kita bahkan bisa juga login sebagai akun bot telegram-cli ini dan melakukan apa yang dapat dilakukan oleh akun normal.

Manfaat bot ini diamini juga oleh pihak Telegram yang kemudian [meluncurkan bot API](https://telegram.org/blog/bot-revolution) agar orang banyak dapat membangun bot menggunakan bahasa pemrograman yang mereka kuasai tanpa harus berhubungan dengan telegram-cli atau MTProto.
Bot API adalah akun bot, ada hal-hal tertentu yang bisa dilakukan akun normal yang tidak bisa dilakukan akun bot, misal membuat group, memasukkan orang ke dalam group dan mengeluarkan orang dari group.

Berikut tautan yang semoga berguna bagi Anda yang berminat mendalami seluk-beluk pembuatan bot Telegram:

- [https://telegram.org/blog/bot-revolution](https://telegram.org/blog/bot-revolution)
- [https://core.telegram.org/bots](https://core.telegram.org/bots)
- [https://core.telegram.org/bots/api](https://core.telegram.org/bots/api)
- [http://web.neurotiko.com/bots/2015/07/21/bots-introduction/](http://web.neurotiko.com/bots/2015/07/21/bots-introduction/)
- [http://web.neurotiko.com/bots/2015/08/03/bots-know-your-api/](http://web.neurotiko.com/bots/2015/08/03/bots-know-your-api/)
- [http://web.neurotiko.com/bots/2015/08/09/bots-coding-simple-bot/](http://web.neurotiko.com/bots/2015/08/09/bots-coding-simple-bot/)
- [Artikel Telegram di http://unnikked.ga](http://unnikked.ga/tags/#telegram)
- [https://github.com/yukuku/telebot](https://github.com/yukuku/telebot)
- [Collection of API wrappers](https://redd.it/4ly273)
- [Master List of Telegram Bots](https://redd.it/3b0glc)
- [Full guide on creating statefull Telegram bot](https://medium.com/@MaximAbramchuk/full-guide-on-creating-statefull-telegram-bot-523def0a7930#.h30ij2aab)
- [https://situsali.com/membuat-bot-telegram/](https://situsali.com/membuat-bot-telegram/)
- [https://situsali.com/membuat-bot-telegram-dengan-php-metode-long-polling/](https://situsali.com/membuat-bot-telegram-dengan-php-metode-long-polling/)

Berikut grup Telegram pengembangan bot:

- Grup [telegram-bot](https://github.com/yagop/telegram-bot): [Telegram Bots Development](https://telegram.me/tgbotchat).
- Grup [otouto](https://github.com/topkecleon/otouto): [Bot Development](https://telegram.me/BotDevelopment).
- [Telegram Bots Development - Indonesia](https://telegram.me/joinchat/BF0grwHZBc9is8g6gI7fUQ)

Berikut tautan repo bot yang banyak digunakan:

- [https://github.com/yagop/telegram-bot](https://github.com/yagop/telegram-bot)
- [https://github.com/topkecleon/otouto](https://github.com/topkecleon/otouto)
- [https://github.com/uziins/uzzbot](https://github.com/uziins/uzzbot)
- [https://github.com/SEEDTEAM/TeleSeed](https://github.com/SEEDTEAM/TeleSeed)

_Shameless plug, here's merbot_: [https://github.com/rizaumami/merbot](https://github.com/rizaumami/merbot).

