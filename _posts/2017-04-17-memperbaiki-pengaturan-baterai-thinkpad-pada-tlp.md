---
layout: post
title: Memperbaiki Pengaturan Baterai ThinkPad pada TLP
tags: [tlp, thinkpad]
comments: true
---

Alkisah, tatkala masyuk menggunakan ThinkPad kesayangan, mata terantuk pada indikator baterai yang menyatakan _full charge_. _Well, that's weird_ karena saya telah menetapkan persentase baterai maksimum 80% jika diisi.

_FYI_, baterai ThinkPad memiliki pengaturan _charged and discharged threshold_ yang gunanya untuk menjaga baterai tetap sehat dan berumur panjang dengan cara menetapkan ambang batas kapan baterai mesti diisi dan batas kapan pengisian baterai mesti dihentikan.  
Saya menggunakan [`tlp`](http://linrunner.de/en/tlp/tlp.html) untuk menetapkan _start charging_ pada 75% dan _stop charging_ pada 80%, yang artinya baterai akan mulai diisi jika kapasitasnya turun hingga 75% dan pengisian akan dihentikan ketika kapasitas baterai mencapai 80%. Jadi ketika melihat bahwa baterai telah terisi 100% menyeruak rasa curiga dalam hati, pasti ada yang tidak beres. Semoga bukan masalah di baterainya.

Mungkinkah karena sesuatu hal [`tlp`](http://linrunner.de/en/tlp/tlp.html) tidak jalan atau terhenti? _Lets start it again_...

```bash
sudo tlp start
```

_Welp, no dice...._ Mungkin laptop perlu di-*restart* mengingat _uptime_-nya telah berhari-hari, siapa tahu ada _update_ yang memerlukan _reboot_ (meski kecil kemungkinannya, _because, you know, it's Linux_). Baiklah, tiada salah untuk mencoba...

...

_Shit happens...._ Pengisian baterai tetap melebihi 80%.  
Bagaimana sekarang? Hmmm... Saatnya untuk mencari jawaban di internet.
Saran pertama adalah melihat pengaturan baterai pada [`tlp`](http://linrunner.de/en/tlp/tlp.html) untuk melihat sekiranya ada yang "aneh". Mari kita coba.

```bash
sudo tlp-stat -b
```

Dan hasilnya.

```bash
+++ ThinkPad Extended Battery Functions
tp-smapi   = inactive (kernel module 'tp_smapi' load error)
tpacpi-bat = inactive (kernel module 'acpi_call' load error)
```

Aaahhh... Jadi inikah biang-keroknya?  
Kalau modul kernel yang tadinya normal namun kemudian mendadak tidak bisa dimuat biasanya karena ada _update_ kernel dan modul gagal dibangun ulang. Kernel baru, modul lama, jadi tidak akur.

Lalu bagaimana cara pemecahannya? Kita coba cara yang paling gampang dulu, copot lalu pasang ulang (_reinstall_) paket-paket yang dirasa bermasalah, yakni `tlp`, `acpi_call` dan `tp-smapi`. 

```bash 
sudo aptitude purge tlp acpi-call-dkms tp-smapi-dkms
```

Untuk lebih memastikan, saya menjalankan perintah _purge_ untuk berkas konfigurasi yang mungkin tidak terhapus oleh perintah di atas. Pastinya mesti membuat ulang berkas konfig ini agar sesuai dengan _hardware_ ThinkPad yang saya miliki, namun tidak masalah... 

```bash
sudo aptitude purge ~c
```

Pasang kembali paket-paket yang tadi dibuang.

```bash 
sudo aptitude install tlp acpi-call-dkms tp-smapi-dkms
```

Yaay, pas _install_ ada _log_ DKMS yang membangun ulang modul. Ini sebuah pertanda baik.  
OK, _let's start the [`tlp`](http://linrunner.de/en/tlp/tlp.html) again..._ dan jalankan `tlp-stat -b`.

```bash
+++ ThinkPad Extended Battery Functions
tp-smapi   = active
tpacpi-bat = active
```

Alhamdulillah...  
Setelah menjalani beberapa kali _charged and discharged_, dapat dipastikan bahwa pengisian baterai telah kembali sesuai pengaturan; terhenti di sekitar 79%.

_The power of restart and reinstall has brought back my sanity..._  


