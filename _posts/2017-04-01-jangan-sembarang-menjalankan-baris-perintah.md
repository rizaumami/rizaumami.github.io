---
layout: post
title: Jangan Sembarang Menjalankan Baris Perintah di Linux
tags: [cli]
comments: true
---

Komputer yang bertenaga dan sistem operasi berkeamanan ketat, akan tiada berarti jika si penggunanya adalah pribadi yang ceroboh. Manusia adalah makhluk sosial, yang mudah terpengaruh suasana hati dan lingkungan. Kerentanan inilah yang dimanfaatkan _malware_ (_malicious software_) dengan teknik _social engineering_-nya.

Kali ini kita tidak akan membahas _malware_, namun temanya masih berkaitan. Menyambung artikel [Jangan Copy Paste dari Website ke Terminal!]({{ site.baseurl }}{% post_url 2017-03-29-jangan-copy-paste-dari-website-ke-terminal %}), artikel kali ini masih bertema ajakan agar tidak ceroboh menjalankan sembarang perintah yang didapat dari dunia maya.

Ada banyak alasan kita bersukarela menjalankan perintah yang kita lihat dari forum atau situs web di komputer kita, misalnya saja karena telah letih _troubleshooting_ kerusakan atau kita percaya kepada si pemberi _command line_. Dengan kata lain, celah kerusakan ini tidak selalu karena si pengguna tidak mengerti dengan apa yang diketiknya, namun lebih cenderung sedang dalam kelengahan. 

Berikut beberapa contoh perintah yang patut diwaspadai dan jangan sembarang dijalankan:

- **Perintah yang menggunakan [_regular expression_](https://en.wikipedia.org/wiki/Regular_expression)**  
  _Regular expression is black magic_. Jika tidak bisa memahami kerja perintah yang memakai _regex_, lebih baik jangan dijalankan.
  
  ```bash
  rm -rf /
  rm -rf .
  rm -rf *
  rm -r .[^.]* 
  ```
  
- **Perintah yang berhubungan dengan sarana penyimpanan**  
  Baca berkali-kali jika Anda diminta menjalankan perintah yang berhubungan dengan partisi atau media penyimpanan lainnya.
  
  ```bash
  mkfs
  mkfs.ext3
  mkfs.apapun_yang_umumnya_nama_filesystem
  sembarang_perintah > /dev/sda
  dd if=something of=/dev/sda_atau_nama_disk_lainnya
  ``` 
  
- **Perintah yang menjalankan skrip**  
  Belum lama ini saya perlu memasang paket `nodejs` teranyar di Debian. Dengan alasan laju pengembangan `nodejs` yang sangat pesat, versi paket dalam repo Debian cenderung lambat diperbaharui, memaksa saya untuk mencari sumber lain.  
  
  Akhirnya saya menuju situs [nodejs.org](https://nodejs.org/en/download/package-manager/). Namun sungguh tak dinyana, proyek sebesar `nodejs` ternyata menyediakan cara pemasangan yang patut dipertanyakan, seperti berikut:
  
  ```bash
  curl -sL https://deb.nodesource.com/setup_7.x | sudo -E bash -
  sudo apt-get install -y nodejs
  ```
  
  Beruntung, `nodejs` juga menyediakan repo tersendiri sehingga saya bisa memasangnya dengan cara yang lebih terjamin keamanannya. 
  
  Intisarinya, teliti siapa dibalik pemberi perintah dengan pola berikut:
  
  ```bash
  wget http://alamat_situs/skrip_berpotensi_membahayakan
  sh ./skrip_berpotensi_membahayakan
  ```
  
  atau 
  
  ```bash
  wget http://alamat_situs/skrip_berpotensi_membahayakan -O- | sh
  ```
  
- **Perintah yang mengandung _string_ yang telah dirubah bentuk (_encoded_)**  
  Anda bertanya di suatu forum untuk meminta saran agar sistem Linux Anda lebih gegas. Dan seseorang kemudian mengirim dan menyarankan untuk menjalankan perintah berikut:
  
  ```bash
  $(echo cm0gLXJmIH4vKg== | base64 -d)
  ```
  
  Apa yang akan Anda lakukan? Tidakkah rentetan huruf itu tampak aneh?  
  Seperti yang dicamkan di atas, jangan jalankan perintah yang tidak Anda mengerti!  
  Baris di atas sekilas tidak terlihat membahayakan, hanya `echo`. Namun SALAH BESAR! Perintah di atas sama dengan `rm -rf ~/*` yang tanpa ragu akan menghapus seluruh isi `$HOME`. Saya lebih memilih kehilangan root daripada kehilangan isi `$HOME`.
  
_So_, berhati-hatilah selalu. _Trust nobody_. Jangan malu untuk bertanya jika mendapati baris perintah yang tidak dimengerti. Jika tidak juga kunjung paham, jangan jalankan.
  
_Permios..._
