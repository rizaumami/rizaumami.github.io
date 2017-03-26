---
layout: post
title: Menghitung Usia Kini Menggunakan LibreOffice Calc
tags: [libreoffice, office, spreadsheet, calc, umur]
comments: true
---

Selain mengelompokkan usia anak, seorang guru kelas juga mesti menghitung usia anak saat ini. Usia seseorang tentunya bertambah setiap saat, jadi daripada tiap bulan menghitung usia secara manual baiknya kita membuat *spreadsheet* yang otomatis menghitung usia anak secara waktu nyata (*real time*).

*Spreadsheet* berikut akan mencontohkan bagaimana menghitung usia anak berdasarkan tanggal lahir di kolom F (F3:F7) dan menempatkan berapa tahun usia anak di kolom G, lewat berapa bulan di kolom H dan lewat berapa hari di kolom I:

[![LibreCalc]({{ site.baseurl }}/assets/img/calc-hitung-usia-kini.png)]({{ site.baseurl }}/img/calc-hitung-usia-kini.png)

Untuk menghitung berapa tahun usia saat ini, gunakan formula berikut di kolom G3:

<pre>
=DATEDIF(F3;TODAY();"Y")
</pre>

Untuk menghitung berapa bulan lewat dari tahun usia saat ini, gunakan formula berikut di kolom H3:

<pre>
=DATEDIF(F3;TODAY();"YM")
</pre>

Untuk menghitung berapa hari lewat bulan usia saat ini, gunakan formula berikut di kolom I3:

<pre>
=DATEDIF(F3;TODAY();"MD")
</pre>
