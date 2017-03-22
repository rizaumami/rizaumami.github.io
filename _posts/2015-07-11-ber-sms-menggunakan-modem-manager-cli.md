---
layout: post
title: Ber-SMS Menggunakan ModemManager CLI
tags: [modem]
comments: true
---

*Why* `mmcli`? *Well, love me some* `modem-manager-gui`, namun apa daya `modem-manager-gui` v0.0.17.1-2 di Debian Sid selalu *crash* saat *start*. Mencoba mandiri mengkompilasi dari *source* plus *patch* ternyata selalu gagal. *So, yeah*, sambil menunggu rotan kini sementara menggunakan akar yang ada.

RTFM. Manual `mmcli` terperinci dan disertai contoh penggunaan. Artikel ini hanya akan membahas sekilas saja.

* Lihat alokasi nomor modem. Entah mengapa di komputer ini alokasi untuk WWAN Gobi 3000 Sierra MC8355, alokasi nomornya selalu berubah.

{% highlight sh %}
mmcli -L
{% endhighlight %}

Misal keluarannya adalah sebagai berikut:

{% highlight sh %}
Found 1 modems:
  /org/freedesktop/ModemManager1/Modem/4 [Sierra Wireless Inc] Sierra Wireless MC8355 - Gobi 3000(TM) Module  
{% endhighlight %}

Berarti nomor modemnya adalah **4**.

* Aktifkan modem

{% highlight sh %}
sudo mmcli -m 4 --enable
{% endhighlight %}

Kini kita siap ber-SMS menggunakan `mmcli`.

## Membaca SMS

### Membaca status SMS

{% highlight sh %}
sudo mmcli -m 4 --messaging-status
{% endhighlight %}

Keluaran akan seperti berikut:

{% highlight sh %}
/org/freedesktop/ModemManager1/Modem/4
  ----------------------------
  Messaging | supported storages: 'sm, me'
            |    default storage: 'me'
{% endhighlight %}

Yang berarti modem mendukung sarana penyimpanan pesan pada kartu SIM (`sm`) dan perangkat modem (`me`). Dari keluaran di atas diketahui juga tempat penyimpanan asali adalah di `me`.

### Membaca daftar SMS

{% highlight sh %}
sudo mmcli -m 4 --messaging-list-sms
{% endhighlight %}

Keluarannya akan serupa dengan:
{% highlight sh %}
Found 13 SMS messages:
  /org/freedesktop/ModemManager1/SMS/13 (received)
  /org/freedesktop/ModemManager1/SMS/14 (received)
  /org/freedesktop/ModemManager1/SMS/15 (received)
  /org/freedesktop/ModemManager1/SMS/16 (received)
  /org/freedesktop/ModemManager1/SMS/17 (received)
  /org/freedesktop/ModemManager1/SMS/18 (received)
  /org/freedesktop/ModemManager1/SMS/19 (received)
  /org/freedesktop/ModemManager1/SMS/20 (received)
  /org/freedesktop/ModemManager1/SMS/21 (received)
  /org/freedesktop/ModemManager1/SMS/22 (received)
  /org/freedesktop/ModemManager1/SMS/23 (received)
  /org/freedesktop/ModemManager1/SMS/24 (received)
  /org/freedesktop/ModemManager1/SMS/25 (received)
{% endhighlight %}

Tampak bahwa ada 13 SMS yang diterima dan tidak ada yang telah dikirim.
Nomor SMS akan berubah tiap kali modem *enabled-disabled*.

### Membaca SMS

Misal untuk membaca SMS nomor 20.
{% highlight sh %}
sudo mmcli -s 20
{% endhighlight %}

Hasil bacaannya tampak seperti berikut:
{% highlight sh %}
SMS '/org/freedesktop/ModemManager1/SMS/20'
  -----------------------------------
  Content    |              number: 'INDOSAT'
             |                text: 'Pkt Super Internet Kuota Rp29.9rb/30hr Anda brakhir pd 23-06-2015 22:21.Pastikan pulsa cukup utk prpanjangan agar tdk trkena tarif internet retail.Info pkt trbaru www.indosat.com'  
  -----------------------------------
  Properties |            PDU type: 'deliver'
             |               state: 'received'
             |             storage: 'me'
             |                smsc: '+62816124'
             |           timestamp: '150623081914+07'
{% endhighlight %}

## Mengirim SMS

Mengirim SMS dilakukan dalam 3 langkah; (1) membuat, (2) menyimpan, dan (3) mengirim SMS.

### Membuat SMS

Misal kita akan memeriksa sisa pulsa Indosat Mentari melalui SMS; yakni dengan mengirim pesan **USAGE** ke nomor **363**.

{% highlight sh %}
mmcli -m 4 --messaging-create-sms="text='USAGE',number='363'"
{% endhighlight %}

Anda mungkin akan ditanya *password*. Jika berhasil akan tampak pesan:

{% highlight sh %}
Successfully created new SMS:
  /org/freedesktop/ModemManager1/SMS/26 (unknown)
{% endhighlight %}

### Menyimpan SMS

Ingat nomor pesan dari langkah membuat SMS (`26`) dan sarana penyimpanan SMS (`me`).

{% highlight sh %}
sudo mmcli -s 26 --store-in-storage="me"
{% endhighlight %}

Jika berhasil akan tampil pesan `successfully stored the SMS`.

### Mengirim SMS

{% highlight sh %}
sudo mmcli -s 26
{% endhighlight %}

Jika berhasil, akan tampil pesan:

{% highlight sh %}
SMS '/org/freedesktop/ModemManager1/SMS/26'
  -----------------------------------
  Content    |              number: '363'
             |                text: 'USAGE'
  -----------------------------------
  Properties |            PDU type: 'submit'
             |               state: 'stored'
             |             storage: 'me'
             |     delivery report: 'not requested'
{% endhighlight %}

Jika kita lihat kembali daftar SMS:

{% highlight sh %}
mmcli -m 4 --messaging-list-sms
{% endhighlight %}

Kita akan lihat SMS telah bertambah:
{% highlight sh %}
Found 14 SMS messages:
  /org/freedesktop/ModemManager1/SMS/13 (received)
  /org/freedesktop/ModemManager1/SMS/14 (received)
  /org/freedesktop/ModemManager1/SMS/15 (received)
  /org/freedesktop/ModemManager1/SMS/16 (received)
  /org/freedesktop/ModemManager1/SMS/17 (received)
  /org/freedesktop/ModemManager1/SMS/18 (received)
  /org/freedesktop/ModemManager1/SMS/19 (received)
  /org/freedesktop/ModemManager1/SMS/20 (received)
  /org/freedesktop/ModemManager1/SMS/21 (received)
  /org/freedesktop/ModemManager1/SMS/22 (received)
  /org/freedesktop/ModemManager1/SMS/23 (received)
  /org/freedesktop/ModemManager1/SMS/24 (received)
  /org/freedesktop/ModemManager1/SMS/25 (received)
  /org/freedesktop/ModemManager1/SMS/26 (stored)
{% endhighlight %}

Untuk melihat balasan SMS kita mesti rajin memeriksa daftar SMS karena `mmcli` tidak memiliki *notification*.

*That's it, hope you find it usefull* ( ͡° ͜ʖ ͡°)
