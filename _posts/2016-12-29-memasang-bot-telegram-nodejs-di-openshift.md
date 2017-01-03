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
Setelah selesai, kita aktifkan webhook bot API Telegram menuju aplikasi ini. Misal nama aplikasi adalah `pegelcurhat` dan nama domain (_username_ openshift) adalah `merbot`, perintahnya adalah sebagai berikut:  

```sh
curl 'https://api.telegram.org/botTOKENBOTAPIDISINI/setWebhook?url=https://pegelcurhat-merbot.rhcloud.com'
```

- Secara _default_, `rhc` akan otomatis melakukan _clone_ terhadap aplikasi yang baru dibuat ini. Sekarang saatnya masuk ke _folder_ hasil _clone_ ini. 
 
```sh
cd pegelcurhat
```

- Pasang modul yang diperlukan; `node-telegram-bot-api` untuk Telegram bot API, `string-hash` untuk menyamarkan identitas pengirim pesan, dan `escape-html` untuk mengganti karakter yang serupa _tag_ HTML agar tidak mengacaukan pesan.  

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
var target = -1001234567890; // ganti ke msg.chat.id target  
// Lihat https://developers.openshift.com/en/node-js-environment-variables.html
var port = process.env.OPENSHIFT_NODEJS_PORT;
var host = process.env.OPENSHIFT_NODEJS_IP;
var domain = process.env.OPENSHIFT_APP_DNS;
var bot = new TelegramBot(token, {webHook: {port: port, host: host}});
// OpenShift enroutes :443 request to OPENSHIFT_NODEJS_PORT
bot.setWebHook(domain+':443/bot'+token);

// jika pesan adalah pesan teks
bot.onText(/(.+)/, function (msg, match) {
  // pesan dikirim melalui PM dan bukan hasil forward 
  if ((msg.chat.type == "private") && !(msg.forward_from)) {
    var text = msg.text;
    // dapatkan tanggal pesan agar tidak perlu berurusan dengan locale
    var d = new Date(msg.date);
    // kalikan tahun dengan bulan dan tanggal
    var rnd = d.getFullYear() * d.getMonth() * d.getDate();
    // kurangi user_id oleh hasil perkalian di atas
    var userId = msg.from.id - rnd;
    // hash hasil pengurangan di atas untuk menyamarkan identitas si pengirim pesan
    var hashedId = hash(userId.toString());
    
    // jika bot baru dihubungi atau user mengetik /start
    if (text.match(/^\/start/)) {
      // rubah karakter pada nama yang serupa tag html
      var name = escapeHtml(msg.from.first_name);
      var welcome = '<b>Hai ' + name + '</b>,\n'
        + 'Kamu bisa langsung ketikkan pesan dan kirim, Mimin akan teruskan pesan kamu ke @pegelcurhat.\n\n'
        + 'Pesan kamu akan Mimin beri <i>tag</i> <code>#id</code>, selain agar gampang dibedakan dari pesan orang lain juga agar bisa diindeks hingga mudah diikuti alurnya.\n'
        + 'Jika kamu tidak suka dengan <i>tag</i> tersebut dan ingin lebih <i>anonymous</i>, awali pesan kamu dengan <code>/noid</code>.';

      bot.sendMessage(msg.chat.id, welcome, {parse_mode: 'HTML'});
    // jika user tidak ingin pesannya diakhiri hashed id
    } else if (text.match(/^\/noid (.+)/)) {
      var curhat = text.replace("/noid", "");
      bot.sendMessage(pegelcurhat, curhat, {parse_mode: 'Markdown', disable_web_page_preview: 'true'});
    // selain pengecualian di atas, kirim seluruh isi pesan dengan diakhiri hashed ID
    } else {
      var curhat = text + '\n\n#id' + hashedId;
      bot.sendMessage(pegelcurhat, curhat, {parse_mode: 'Markdown', disable_web_page_preview: 'true'});
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
_private message_ si bot API, dan ketikkan pesan kemudian kirimkan, maka si bot akan meneruskan pesan tersebut ke grup target. Pesan yang telah dikirim akan diberi _tag_ `#id` + _hashed id_ sebagai penanda agar pesan tetap unik dan bisa diindeks hingga mudah diikuti alurnya.  Jika Anda risih dan merasa kurang _anonymous_ karenanya, awali pesan Anda dengan `/noid`, maka pesan tersebut tidak memiliki ciri pembeda hingga membuatnya lebih _anonymous_. 

_That's it, have a nice_ curhat...  
Jika berminat, Anda bisa bergabung dengan [@pegelcurhat](https://telegram.me/pegelcurhat) dan atau menyampaikan curhat Anda ke [@temancurhat_bot](https://telegram.me/temancurhat_bot).
