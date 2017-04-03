---
layout: post
title: Mengubah Angka Menjadi Teks di LibreOffice Calc
tags: [libreoffice]
comments: true
---

Mengisi buku laporan pendidikan, atau biasa disebut rapor, adalah hal rutin yang mesti dilakukan seorang guru kelas di tiap semester. Untuk tiap mata pelajaran, buku rapor diisi dengan penilaian berupa angka dan huruf bilangan angka tersebut. Artikel kali ini akan bisa membantu pekerjaan pengisian buku rapor. _Well_, bukan itu saja, namun juga pekerjaan-pekerjaan lain yang perlu mengubah angka menjadi teks terbilang, misalnya membuat kuitansi.

Bagaimana cara menampilkan teks dari suatu angka? Kita melakukannya dengan bantuan `LibreOffice` _extension_ `NUMBERTEXT` yang memiliki dua fungsi; `NUMBERTEXT` dan `MONEYTEXT`.  
`NUMBERTEXT` dan `MONEYTEXT` adalah _spreadsheet functions_ untuk mengubah angka ke bilangan dan bilangan uang  sesuai bakuan [OpenFormula](https://en.wikipedia.org/wiki/OpenFormula).

Ikuti langkah berikut untuk memasang dan menggunakan ekstensi `NUMBERTEXT`:

- Unduh `NUMBERTEXT` _extension_ dari <https://extensions.libreoffice.org/extensions/numbertext-1>.
- Pasang`NUMBERTEXT` _extension_ menggunakan salah satu cara di bawah ini:
  - Langsung buka ekstensi menggunakan `LibreOffice`.
  - Melalui menu **Tools ➜ Extension Manager... ➜ Add...**

Lihat gambar berikut untuk contoh `NUMBERTEXT` dalam tabel rapor.

[![numbertext]({{ site.baseurl }}/assets/img/numbertext.png)]({{ site.baseurl }}/assets/img/numbertext.png)

Untuk mengubah angka dalam sel `E9` menjadi teks di `F9`, masukkan formula berikut di `F9`:

```
=NUMBERTEXT(E9;"id-ID")
```

Dan berikut adalah contoh menggunakan `MONEYTEXT` untuk aplikasi kuitansi:

[![moneytext]({{ site.baseurl }}/assets/img/moneytext.png)]({{ site.baseurl }}/assets/img/moneytext.png)

Untuk mengubah nilai uang di `D2` menjadi uang terbilang di `D3`, masukkan formula berikut di `D3`:

```
=MONEYTEXT(D2;"IDR")
```

_No need Excel to be excell on spreadsheet, isn’t it?_ 🙂
