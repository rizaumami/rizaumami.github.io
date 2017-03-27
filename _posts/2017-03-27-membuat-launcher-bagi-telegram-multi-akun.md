---
layout: post
title: Membuat Launcher Bagi Telegram Multi Akun
tags: [telegram, bash, script]
comments: true
---

Sekitar lebih dari setahun yang lalu, saya menulis sebuah artikel berjudul [Menjalankan Banyak Akun Telegram Secara Bersamaan Menggunakan Telegram Desktop di Linux]({{ site.baseurl }}{% post_url 2016-02-10-menjalankan-banyak-akun-telegram-secara-bersamaan-menggunakan-telegram-desktop %}) karena saya sering merasa kerepotan menjalankan akun-akun Telegram saya secara bersamaan.

Mulanya saya merasa cukup membuat alias bagi perintah-perintah untuk menjalankan tiap akun. Namun kini cara tersebut terasa merepotkan dan membatasi karena alias mesti dijalankan melalui terminal. Dalam artikel kali ini, saya akan menunjukkan bagaimana membuat _desktop launcher_ bagi Telegram Desktop multi akun, hingga selanjutnya bisa menjalankan akun-akun tersebut tanpa harus mengetikkan perintah dalam terminal. _Bonus point, launcher_ ini akan tetap bisa dijalankan melalui terminal, _so, a win-win solution..._

### _Launcher script_

Pertama, buat sebuah skrip yang akan menjalankan masing-masing akun Telegram. Misal namanya adalah `tdesktop` dan diletakkan di map `/home/anu/scripts`.

```bash
#!/bin/bash

TDESKTOP_DIR="$HOME/Software/internet/Telegram/tdesktop"

# MAIN -------------------------------------------------------------------------

cd "$TDESKTOP_DIR" || exit

if [ ! -d "$1" ]; then
  unset AKUN i
  while IFS= read -r -d $'\0' folder; do
    AKUN[i++]="''"
    AKUN[i++]="${folder##*/}"
  done < <(find $TDESKTOP_DIR/ -maxdepth 1 -type d -print0)

  MENU=$(zenity \
    --width=400 \
    --height=200 \
    --list \
    --radiolist \
    --column='' \
    --column='Telegram Account' "${AKUN[@]:2}" \
    --text='Pilih akun yang akan Anda jalankan' \
    --title="Telegram Desktop Launcher" 2>/dev/null)

  if [[ $? -eq 0 ]]; then
    for pilihan in $MENU; do
      ./Telegram -many -workdir ./"$pilihan" &>/dev/null &
    done
  fi
else
  ./Telegram -many -workdir ./"$1" &>/dev/null &
fi
```

Berikut penjelasan isi skrip di atas:

```bash
TDESKTOP_DIR="$HOME/Software/internet/Telegram/tdesktop"
```
Baris ini diisi dengan letak direktori tempat kita menyimpan _configuration folders_ Telegram Desktop. Dicontohkan letaknya di `$HOME/Software/internet/Telegram/tdesktop`.

```bash
if [ ! -d "$1" ]; then
```
Baris ini untuk menentukan manakah yang mesti dijalankan, menu pilihan akun atau Telegram Desktop. Jika skrip menerima perintah tanpa parameter, maka ia akan menjalankan menu pilihan akun, jika perintah disertai parameter berupa nama _folder_ akun, maka ia akan langsung menjalankan Telegram Desktop untuk akun tersebut.

```sh
unset AKUN i
while IFS= read -r -d $'\0' folder; do
  AKUN[i++]="''"
  AKUN[i++]="${folder##*/}"
done < <(find $TDESKTOP_DIR/ -maxdepth 1 -type d -print0)
```
Baris ini untuk mendaftar _configuration folders_ dan menjadikannya sebuah `bash` _array_. 

```bash
MENU=$(zenity \
  --width=400 \
  --height=200 \
  --list \
  --radiolist \
  --column='' \
  --column='Telegram Account' "${AKUN[@]:2}" \
  --text='Pilih akun yang akan Anda jalankan' \
  --title="Telegram Desktop Launcher" 2>/dev/null)
```
Baris ini menggunakan `zenity` untuk membuat menu GUI pilihan akun. `zenity` digunakan karena karena ia sudah ada dalam DE yang kini saya pakai; Gnome. Jika Anda menggunakan DE lain dan khawatir `zenity` akan membawa banyak _dependencies_, silahkan coba `yad` dan sesuaikan skripnya.

```bash
if [[ $? -eq 0 ]]; then
  for pilihan in $MENU; do
    ./Telegram -many -workdir ./"$pilihan" &>/dev/null &
  done
fi
```
Baris ini untuk menjalankan hasil pilihan dari menu GUI.

```bash
else
  ./Telegram -many -workdir ./"$1" &>/dev/null &
fi
```
Jika skrip dijalankan disertai parameter berupa nama _folder_ akun, maka bagian inilah yang akan dieksekusi.
  
Sampai tahap ini, skrip sudah mencukupi untuk dapat menjalankan Telegram Desktop banyak akun. Namun karena berupa skrip, maka ia tidak akan muncul dalam menu _Desktop Environment_.  

### _Desktop Launcher_

Agar skrip _launcher_ di atas bisa muncul dalam menu DE, maka kita perlu untuk membuatkannya sebuah _.desktop launcher_. Misal namanya adalah `tdesktop.desktop`.

```bash
[Desktop Entry]
Encoding=UTF-8
Version=1.0
Name=Telegram Desktop
Comment=Official desktop version of Telegram messaging app
Exec=/home/anu/scripts/tdesktop
Icon=telegram
Terminal=false
StartupWMClass=Telegram
Type=Application
Categories=Network;
MimeType=x-scheme-handler/tg;
X-Desktop-File-Install-Version=0.23
``` 
Perhatikan baris `Exec`, isinya harus sesuai dengan letak _launcher script_ di atas.   
Simpan berkas `.desktop` ini di `$HOME/.local/share/applications`, atau agar bisa diakses semua _user_, simpan di `/usr/share/applications`. Jangan lupa untuk menjadikannya _executable_.


Demikianlah. Sekarang kita bisa menjalankan Telegram untuk akun yang diinginkan dalam dua cara:

1. Buka terminal, jalankan berkas `tdesktop` diikuti nama map konfigurasi untuk akun yang hendak dijalankan. Misal, untuk menjalankan akun @kuncen yang konfigurasinya ada dalam map `mentari`:

   ```bash
   tdesktop mentari
   ```

2. Jalankan akun melalui menu _Desktop Environment_. Misal, dalam Gnome Shell, klik kunci `SUPER` pada _keyboard_ dan ketik "telegram" sampai muncul ikon Telegram, pilih ikon tersebut dan tekan `ENTER`, maka menu untuk memilih akun akan muncul. Klik pada _radio button_ di kolom paling kanan akun, kemudian klik tombol OK, maka akun yang dipilih pun akan dijalankan.

_Happy cloning..._
