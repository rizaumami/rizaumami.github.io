---
layout: post
title: Memasang Bot Telegram NodeJS di OpenShift
tags: [telegram, bot, node.js, javascript, openshift]
comments: true
---

_At first, I have a plan_...  
Bagaimana jika membuat grup Telegram tempat curhat, berkeluh-kesah, atau menyampaikan pendapat? Mengingat manusia adalah makhluk sosial, yang memerlukan interaksi dengan manusia lain dalam kehidupannya. 

Akhirnya, group [@pegelcurhat](https://t.me/pegelcurhat) pun dibentuk.  
Namun kemudian muncul permasalahan baru; bagaimana dengan kerahasiaan identitas si pengirim pesan? Terlebih jika pesan yang ingin disampaikan bersifat pribadi atau kontoversial?  

Akhirnya saya sampai pada kesimpulan akhir, yakni perlu dibuatkan bot untuk menjembatani si pengirim pesan dengan grup [@pegelcurhat](https://t.me/pegelcurhat). Jadi si pengirim pesan bisa menyampaikan pendapatnya dengan leluasa tanpa khawatir identitasnya diketahui.  
Apakah benar identitas pengirim pesan terjaga jika menggunakan bot ini?  
_Not really_. Saya sebagai pemilik bot bisa _logging_ dan mengetahui _user_ mana yang mengirim _post_ tertentu.  
Namun, saya tidak tertarik melakukan _logging_. Di sini kuncinya, jika tidak ada _trust_ terhadap saya selaku pemilik bot, jangan gunakan bot ini.

Permasalahan selanjutnya adalah bagaimana untuk menjalankan bot ini 24 jam sehari dan 7 hari seminggu, sementara saya tidak memiliki _private virtual server_?  
Untungnya, beberapa waktu yang lalu saya mendaftar akun gratis di [OpenShift](https://www.openshift.com/). Saya katakan beruntung karena sekarang tidak ada akun [OpenShift](https://www.openshift.com/) yang gratis, melainkan hanya _trial_.  
_Here we go, I just need to get to know how to deploy a Telegram bot on OpenShift..._

Setelah mencari dan mencoba banyak tutor dari internet, akhirnya saya mendapatkan sebuah [tutorial untuk memasang bot NodeJS di OpenShift yang relatif mudah dipahami dan bekerja dengan baik](https://github.com/ilbonte/node-telegram-bot-starter-kit).

Berikut ringkasan langkah-langkah yang disarikan dari artikel tersebut:
 
- Siapkan sebuah akun bot dan tokennya. Jika belum punya, hubungi [@BotFather](https://t.me/BotFather).

- Selanjutnya, pasang _dependencies_ yang dibutuhkan untuk membuat _gear_ OpenShift secara lokal.

```sh
sudo aptitude install rhc npm
```

- Buat aplikasi baru di OpenShift. Dicontohkan aplikasi ini diberi nama `pegelcurhat`.  

```sh
rhc create-app pegelcurhat nodejs-0.10
```

- Tunggu proses pembuatan aplikasi selesai.  
Agar bot mendapatkan pesan Telegram, ada dua cara yang bisa digunakan; (1) webhook, dan (2) _polling_. Sederhananya, dengan webhook bot kita seakan-akan mengatakan "Hai, Telegram, kalau ada pesan untuk saya tolong dikirim ke alamat ini ya", sementara jika menggunakan _polling_, bot kita bertanya "Hai, Telegram, apakah ada pesan untuk saya?" secara berulang.  
Metode _polling_ sebenarnya lebih sederhana, namun karena _free gear_ dari OpenShift akan otomatis _suspended_ jika tidak ada _request_ dari luar, maka metode ini tidak cocok digunakan pada akun OpenShift saya yang _free_.  
Setelah pembuatan aplikasi selesai, kita aktifkan webhook bot API Telegram menuju aplikasi ini. Misal nama aplikasinya adalah `pegelcurhat` dan nama domain (_username_ openshift) adalah `merbot`, perintahnya adalah sebagai berikut:  

```sh
curl 'https://api.telegram.org/botTOKENBOTAPIDISINI/setWebhook?url=https://pegelcurhat-merbot.rhcloud.com'
```

- Secara _default_, `rhc` akan otomatis melakukan _clone_ terhadap aplikasi yang baru dibuat ini. Sekarang saatnya masuk ke _folder_ hasil _clone_ tersebut. 
 
```sh
cd pegelcurhat
```

- Pasang modul-modul yang diperlukan, yakni `node-telegram-bot-api` untuk Telegram bot API, `string-hash` untuk menyamarkan identitas pengirim pesan, dan `escape-html` untuk mengganti karakter yang serupa _tag_ HTML agar tidak mengacaukan isi pesan.  

```sh
npm install -S node-telegram-bot-api string-hash escape-html
``` 

- Buka berkas `server.js` dan ganti isinya dengan _code_ berikut. Sesuaikan token bot serta `chat_id` target (misal `chat_id` grup [@pegelcurhat](https://t.me/pegelcurhat)):  

```javascript  
// https://github.com/yagop/node-telegram-bot-api  
var TelegramBot = require('node-telegram-bot-api');  
// https://github.com/darkskyapp/string-hash untuk hashing user_id pengirim pesan.  
var hash = require('string-hash'); 
// https://github.com/component/escape-html untuk escaping tag html.
var escapeHtml = require('escape-html');
// Dapatkan Token dari @BotFather -- https://core.telegram.org/bots#6-botfather  
var token = 'LETAKKAN_TOKEN_BOT_API_DI_SINI';  
// Tujuan pesan akan dikirim (berupa chat_id).  
var pegelcurhat = -1001234567890; // ganti ke msg.chat.id target 
// See https://developers.openshift.com/en/node-js-environment-variables.html
var port = process.env.OPENSHIFT_NODEJS_PORT;
var host = process.env.OPENSHIFT_NODEJS_IP;
var domain = process.env.OPENSHIFT_APP_DNS;
var bot = new TelegramBot(token, {webHook: {port: port, host: host}});
// OpenShift enroutes :443 request to OPENSHIFT_NODEJS_PORT
bot.setWebHook(domain+':443/bot'+token);

// samarkan identitas si pengirim pesan
function hashedId(msg) {
  // dapatkan tanggal pesan agar tidak perlu berurusan dengan locale
  var d = new Date(msg.date);
  // kalikan tahun dengan bulan dan tanggal
  var rnd = d.getFullYear() * d.getMonth() * d.getDate();
    // kurangi user_id oleh hasil perkalian di atas
  var userId = msg.from.id - rnd;
    // hash hasil pengurangan di atas untuk menyamarkan identitas pengirim pesan
  return hash(userId.toString());
}

// Olah caption
function getCaption(msg) {
  // jika pesan memiliki sisipan tulisan
  if (msg.caption) {
    var caption = msg.caption;
    // jika diawali /id
    if (caption.match(/^\/id/)) {
      // hapus /id dan tambahkan tag #id
      var txt = caption.replace('/id', '') + '\n\n#id' + hashedId(msg);
    } else {
      var txt = caption;
    }
  } else {
    var txt = '';
  } 
  return txt;
}

// ambil semua jenis pesan
bot.on('message', function (msg) {
  // jika pesan dikirim melalui private message dan bukan hasil forward
  if ((msg.chat.type == "private") && !(msg.forward_from)) {
    // jika pesan adalah teks
    if (msg.text) {
      var text = msg.text;
      // teks diawali /id
      if (text.match(/^\/id (.+)/)) {
        // hapus /id dan tambahkan tag #id
        var curhat = text.replace('/id', '') + '\n\n#id' + hashedId(msg);
        bot.sendMessage(pegelcurhat, curhat, {parse_mode: 'Markdown', disable_web_page_preview: 'true'});
      // teks diawali /start
      } else if (text.match(/^\/start/)) {
        var name = escapeHtml(msg.from.first_name);
        var welcome = '<b>Hai ' + name + '</b>,\n'
          + 'Kamu bisa langsung ketikkan pesan dan kirim, Mimin akan teruskan pesan kamu ke @pegelcurhat.\n\n'
          + 'Awali pesan kamu dengan <code>/id</code>, maka pesan kamu akan Mimin beri <i>tag</i> <code>#id</code> agar mudah dibedakan dari pesan orang lain dan bisa diindeks hingga mudah diikuti alurnya.';  

        bot.sendMessage(msg.chat.id, welcome, {parse_mode: 'HTML'});
      // selain pengecualian di atas, kirim semua isi pesan
      } else {
        bot.sendMessage(pegelcurhat, text, {parse_mode: 'Markdown', disable_web_page_preview: 'true'});
      }
    // jika pesan adalah dokumen
    } else if (msg.document) {
      var text = getCaption(msg);
      bot.sendDocument(pegelcurhat, msg.document.file_id, {caption: text});
    // jika pesan adalah foto
    } else if (msg.photo) {
      var text = getCaption(msg);
      bot.sendPhoto(pegelcurhat, msg.photo[0].file_id, {caption: text});
    // jika pesan adalah stiker
    } else if (msg.sticker) {
      bot.sendSticker(pegelcurhat, msg.sticker.file_id);
    // jika pesan adalah video
    } else if (msg.video) {
      var text = getCaption(msg);
      bot.sendVideo(pegelcurhat, msg.video.file_id, {caption: text});
    // jika pesan adalah suara
    } else if (msg.voice) {
      var text = getCaption(msg);
      bot.sendVoice(pegelcurhat, msg.voice.file_id, {caption: text});
    // jika pesan adalah lokasi
    } else if (msg.location) {
      var lat = msg.location.latitude;
      var lon = msg.location.longitude;
      bot.sendLocation(pegelcurhat, lat, lon);
    }
  };
});

```
- Saatnya untuk _deploying_...  

```sh
git add .
git commit -m 'Edit server.js dan install node-telegram-bot-api, string-hash, dan escape-html' 
git push
```

- Pemasangan bot telah selesai dan kita bisa mencobanya.  
_private message_ si bot API, dan ketikkan pesan kemudian kirimkan, maka si bot akan meneruskan pesan tersebut ke grup target.  

Karena pesan kita dikirim oleh bot, maka tidak akan bisa dibedakan dengan pesan _user_ lain yang juga dikirimkan oleh bot. Sifat _pseudo anonymous_ ini bisa jadi membingungkan dan membuat pesan Anda susah diikuti. Jika demikian, awali pesan dengan `/id` agar pesan tersebut diberi _tag_ `#id` + _hashed id_ sebagai penanda agar pesan tetap unik dan bisa diindeks hingga mudah diikuti alurnya.

_That's it, have a nice_ curhat...  
Jika berminat, Anda bisa bergabung dengan [@pegelcurhat](https://t.me/pegelcurhat) dan atau menyampaikan curhat Anda ke [@temancurhat_bot](https://t.me/temancurhat_bot).
