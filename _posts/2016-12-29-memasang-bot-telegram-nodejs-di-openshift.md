---
layout: post
title: Memasang Bot Telegram NodeJS di OpenShift
tags: [telegram, bot, node.js, javascript, openshift]
comments: true
---

_At first, I have a plan_...  
Bagaimana jika membuat grup Telegram tempat curhat, berkeluh-kesah, atau menyampaikan pendapat? Mengingat manusia adalah makhluk sosial, yang memerlukan interaksi dengan manusia lain dalam kehidupannya. 

Akhirnya, group [@pegelcurhat](https://telegram.me/pegelcurhat) pun dibentuk.  
Namun kemudian muncul permasalahan baru; bagaimana dengan kerahasiaan identitas si pengirim pesan? Terlebih jika pesan yang ingin disampaikan bersifat pribadi atau kontoversial?  

Akhirnya saya sampai pada kesimpulan akhir, yakni perlu dibuatkan bot untuk menjembatani si pengirim pesan dengan grup [@pegelcurhat](https://telegram.me/pegelcurhat). Jadi si pengirim pesan bisa menyampaikan pendapatnya dengan leluasa tanpa khawatir identitasnya diketahui.  
Apakah benar identitas pengirim pesan terjaga jika menggunakan bot ini?  
_Not really_. Saya sebagai pemilik bot bisa _logging_ dan mengetahui _user_ mana yang mengirim _post_ tertentu.  
Namun, saya tidak tertarik melakukan _logging_. Di sini kuncinya, jika tidak ada _trust_ terhadap saya selaku pemilik bot, jangan gunakan bot ini.

Permasalahan selanjutnya adalah bagaimana untuk menjalankan bot ini 24 jam sehari dan 7 hari seminggu, sementara saya tidak memiliki _private virtual server_?  
Untungnya, beberapa waktu yang lalu saya mendaftar akun gratis di [OpenShift](https://www.openshift.com/). Saya katakan beruntung karena sekarang tidak ada akun [OpenShift](https://www.openshift.com/) yang gratis, melainkan hanya _trial_.  
_Here we go, I just need to get to know how to deploying a Telegram bot on OpenShift..._

Setelah mencari dan mencoba banyak tutor dari internet, akhirnya saya mendapatkan sebuah [tutorial untuk memasang bot NodeJS di OpenShift yang relatif mudah dipahami dan bekerja dengan baik](https://github.com/ilbonte/node-telegram-bot-starter-kit).

Berikut ringkasan langkah-langkah yang disarikan dari artikel tersebut:
 
- Pasang _dependencies_ yang dibutuhkan.  

```sh
sudo aptitude install rhc npm
```

- Buat aplikasi baru di OpenShift. Dicontohkan aplikasi ini diberi nama `pegelcurhat`.  

```sh
rhc create-app pegelcurhat nodejs-0.10
```

- Tunggu proses pembuatan aplikasi selesai.  
Setelah selesai, `rhc` akan otomatis melakukan _clone_ terhadap aplikasi yang baru dibuat ini. Sekarang saatnya masuk ke _folder_ hasil _clone_ ini. 
 
```sh
cd pegelcurhat
```

- Pasang modul yang diperlukan; `node-telegram-bot-api` untuk Telegram bot API, dan `string-hash` untuk menyamarkan identitas pengirim pesan.  

```sh
npm install -S node-telegram-bot-api string-hash
``` 

- Buka berkas `server.js` dan ganti isinya dengan _code_ berikut dan sesuaikan bot token serta `chat_id` target (misal `chat_id` grup [@pegelcurhat](https://telegram.me/pegelcurhat)):  

```javascript  
// https://github.com/yagop/node-telegram-bot-api  
var TelegramBot = require('node-telegram-bot-api');  
// https://github.com/darkskyapp/string-hash untuk hashing user_id pengirim pesan.  
var hash = require('string-hash');  
// Dapatkan Token dari @BotFather -- https://core.telegram.org/bots#6-botfather  
var token = 'LETAKKAN_TOKEN_BOT_API_DI_SINI';  
// Jalankan getUpdates menggunakan metode long polling.  
var bot = new TelegramBot(token, { polling: true });  
// Tujuan pesan akan dikirim (berupa chat_id).  
var target = -1001234567890; // ganti ke msg.chat.id target  

// jika pesan adalah pesan teks
bot.onText(/(.+)/, function (msg, match) {
  var text = msg.text; 
  // dapatkan tanggal pesan
  var d = new Date(msg.date);
  // kalikan tahun dengan bulan dan tanggal
  var rnd = d.getFullYear() * d.getMonth() * d.getDate();
  // kurangi user_id oleh hasil perkalian di atas
  var userId = msg.from.id - rnd;
  // hash hasil pengurangan di atas untuk menyamarkan identitas si pengirim pesan
  var hashedId = hash(userId.toString());
  
  // jika bot baru dihubungi atau user mengetik /start
  if (text.match(/\/start/)) {
    var curhat = 'Hi, kamu bisa langsung ketikkan pesan dan kirim, Mimin akan teruskan pesan kamu ke @pegelcurhat.\n\nJika kamu ingin _anonymous_, awali pesan kamu dengan `/noid`.';  
  // jika user tidak ingin pesannya diakhiri hashed id
  } else if (text.match(/\/noid (.+)/)) {
    var curhat = text.replace("/noid ", "") + '\n\n#noid';
  // selain pengecualian di atas, kirimkan semua pesan
  } else {
    var curhat = text + '\n\n#id' + hashedId;
  }

  // hanya olah pesan yang dikirim melalui private message bot
  if (msg.chat.type == "private") {
    // jika pesan hanya berupa string /start
    if (text.match(/\/start/)) {
      bot.sendMessage(msg.chat.id, curhat, {parse_mode: 'Markdown'});
    // selain itu
    } else {
      bot.sendMessage(pegelcurhat, curhat, {parse_mode: 'Markdown', disable_web_page_preview: 'true'});
      bot.sendMessage(msg.chat.id, "Curhat kamu telah dikirim ke @pegelcurhat 😊");
    };
  };
});

```
- Saatnya untuk _deploying_...  

```sh
git add .
git commit -m 'Install node-telegram-bot-api and string-hash' 
git push
```

Tunggu beberapa saat. Jika muncul _error_ bahwa aplikasi ini tidak mendapatkan _port_, abaikan saja karena bot ini tidak menggunakan _port_ tersebut.  

- Pemasangan bot telah selesai dan kita bisa mencobanya.  
_private message_ si bot API, dan kirimkan pesan diawali dengan _string_ `/curhat`, maka si bot akan _forwarding_ pesan tersebut ke grup target.  

_That's it, have a nice_ curhat...  
Jika berminat, Anda bisa bergabung dengan [@pegelcurhat](https://telegram.me/pegelcurhat) dan atau menyampaikan curhat Anda ke [@temancurhat_bot](https://telegram.me/temancurhat_bot).
