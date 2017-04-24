---
layout: post
title: Nautilus Script untuk Memulai Menulis Blog
tags: [nautilus, zenity]
comments: true
---

Seperti biasa, judul artikel ini pun mungkin membingungkan, jadi izinkan saya untuk sedikit memberi mukadimah.  
Selama ini tiap kali hendak membuat artikel baru, saya selalu membuat berkas `markdown` artikel dan memberi nama secara manual. _Well_, untuk _header_ isinya tidak benar-benar manual karena telah ada _template_-nya di `$HOME/Public`, jadi tinggal klik kanan di _folder_ `_post` repo blog ➜ New Document, lalu pilih _template_ dan memberinya nama sesuai judul. _Yeah_, sedikit repot, namun tidak terasa karena saya tidak kerap menulis artikel.

Dan beberapa hari lalu, saya membaca sebuah artikel dari [blog kawan](https://linhub.io/ruby/2017/04/23/mudah-memasang-ruby/) tentang kisahnya membangun blog statis menggunakan `jekyll`. _His blog is interesting_, sebuah blog statis namun menerapkan banyak pernik yang membuatnya terlalu "sibuk" sebagai sebuah blog statis.  
Kembali menyoal artikelnya. Yang menarik perhatian adalah bagaimana dia menggunakan bantuan `rake` untuk automasi proses pembuatan _template_ artikel. _Sounds useful_. Namun setelah lanjut membaca, ternyata `rake` ini membutuhkan berkas pengaturan `rakefile` yang isinya lumayan rumit. Entah rumit karena memang dasarnya rumit, atau memang kawan saya ini memerlukan pengaturan membuat _template_ yang rada rumit, yang pasti cukup memadamkan ketertarikan saya untuk menggunakannya.

`rake` mungkin agak rumit, namun membuat automasi pembuatan _template_ artikel tetaplah menarik. _Well, I have an idea_. Seperti halnya ketika [membuat _launcher_ Telegram desktop](2017-03-27-membuat-launcher-bagi-telegram-multi-akun.md) di artikel lalu, kali ini pun saya akan membuat sebuh script sederhana yang mendayagunakan _utilities_ yang sudah terpasang dalam sistem, seperti `zenity`, `nautilus`, dan `bash`.  

Tidak berpanjang lebar, buat sebuah skrip `bash` dan letakkan di sembarang tempat asal bisa dipanggil melalui terminal. Berikut isi skripnya, misal kita beri nama `ghpages`.

```bash
#!/bin/bash

# Create Github Pages blog template

# VARIABLES --------------------------------------------------------------------

# Default PATH if script executed in command line
POSTDIR="$HOME/github/blog/rizaumami.github.io/_posts"
# Blog date
DATE=$(date +%F)

# FUNCTIONS --------------------------------------------------------------------

buat_template() {
# Get all the parameters
TITLE="$1"
# Make it lowercase
LTITLE="${TITLE,,}"
# Replace spaces by dash
CTITLE="${LTITLE// /-}"
# Post template
cat << HEAD > "$DATE-$CTITLE.md"
---
layout: post
title: $TITLE
tags: []
comments: true
---

HEAD

xdg-open "$DATE-$CTITLE.md"
}

print_help() {
  printf '\e[1;33m%s\n\e[0;39;49m' '
  Penggunaan:   ghpages [JUDUL ARTIKEL]
  '
  exit
}

# MAIN -------------------------------------------------------------------------

if [ $# -eq 0 ]; then
  JUDUL=$(zenity --entry \
                --title='Judul artikel' \
                --text='Ketik judul artikel yang akan ditulis:' \
                --entry-text '')

  if [[ $? -eq 0 ]]; then
    cd "$(printf ${NAUTILUS_SCRIPT_SELECTED_FILE_PATHS})"
    buat_template "$JUDUL"
  else
    zenity --info --text='<i>Template</i> artikel <b>batal</b> dibuat karena Anda tidak memasukkan judul artikel.'
  fi
else
  case "$1" in
    -h|--help)
      print_help
    ;;
    *)
      cd "$POSTDIR"
      buat_template "$*"
    ;;
  esac
fi
```
Tidak ada yang aneh dari isi skrip-nya, jadi tidak perlu kiranya dijelaskna. Hanya saja, sesuaikan `POSTDIR` dengan letak map artikel. Menggunakan skrip di atas, kita bisa langsung membuat _template_ artikel baru dengan cara memanggilnya dalam terminal menggunakan perintah `ghpages Judul Artikel`. Atau jika menginginkan sentuhan GUI, jalankan skrip tanpa argumen; `ghpages`.

Sekarang, kita tambahkan sedikit sentuhan agar skrip tidak melulu mesti dipanggil melalui terminal, namun bisa melalui _context menu_ (klik kanan). Dan karena saya menggunakan Gnome, maka bisa mendayagunakan `nautilus script`.  
Buat sebuah skrip `nautilus` bernama misalnya "New blog article" dan simpan di `$HOME/.local/share/nautilus/scripts`. Berikut isi skript tersebut

```bash
#!/bin/bash

ghpages
```

Nah, sekarang saya memiliki beberapa cara untuk memulai menulis artikel;
- Menjalankan perintah `ghpages Judul Artikel` di dalam terminal. 
- Klik kanan dalam `nautilus` ➜ Scripts ➜ New blog article, lalu memberi judul artikel.

Kedua langkah di atas akan otomatis membuat berkas artikel dan membukanya dalam penyunting teks.  

_Happy writing..._
