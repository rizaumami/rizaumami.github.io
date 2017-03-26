---
layout: post
title: Mengelompokkan Umur Menggunakan LibreOffice Calc
tags: [libreoffice, office, spreadsheet, sort, calc, umur]
comments: true
---

Sudah rutin tiap bulan bagi guru kelas untuk menghitung kelompok umur anak. *Sounds simple*. Nyatanya, untuk mengelompokkan umur anak kita harus;

- menghitung umur anak tersebut pada bulan ini,
- memisahkan umur anak, misalnya berapa yang berumur 6, 7 atau 8, dan
- menghitung jumlah laki-laki atau perempuan untuk tiap kelompok umur.

_Well, that’s not a rocket science, but sure is annoying_.

_Life is hard enough, don’t make it harder shall we?_. Berikut dicontohkan menggunakan _spreadsheet_ untuk meringankan pekerjaan kita.

Perhatikan gambar di bawah. Kita memiliki sebuah tabel biodata anak dari kolom B baris 2 (B2) hingga kolom H baris 12 (H12). Kelompok umur akan diletakkan di J2 hingga M6. Fokus utama tentu saja hanya di K3 hingga M4 karena untuk K5 hingga M6 hanyalah penjumlahan biasa.

[![LibreCalc]({{ site.baseurl }}/assets/img/calc_group_age.png)]({{ site.baseurl }}/img/calc_group_age.png)

Bagaimana misalnya mengisi sel K3 dengan jumlah anak laki-laki yang berusia 6 tahun? Gunakan formula berikut pada sel K3:

<pre>
=SUMPRODUCT((C3:C12=J3);(F3:F12=K2))
</pre>

Formula tersebut dibaca; lihat sel C3 hingga C12 (C3:C12), apakah terdapat sel yang bernilai sama dengan sel J3? (L, yang menunjukkan laki-laki), jika ya, lihat sel F3 hingga F12 (F2:F12) dan hitung jumlah sel bernilai sama dengan sel K2 (6, yang menunjukkan usia anak).

_That’s it_. Ulangi formula pada sel K3 hingga M4. Namun kita tidak bisa asal _copy-paste_ formula di atas karena referensinya akan berubah. Berikut formula yang memungkinkan kita _copy-paste_ atau menyeret sudut sel untuk mengisi kolom lain secara otomatis.

<pre>
=SUMPRODUCT(($C$3:$C$12=$J3);($F$3:$F$12=K$2))
</pre>

Tanda dollar (_dollar sign_; `$`) membuat sel referensi menjadi statis. Jadi, walau disalin ke sel manapun referensinya akan tetap mengarah pada sel tersebut.
