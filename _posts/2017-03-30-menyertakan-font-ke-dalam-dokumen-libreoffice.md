---
layout: post
title: Menyertakan Font ke Dalam Dokumen LibreOffice
tags: [libreoffice]
comments: true
---

Anda sering bertukar dokumen? Jika ya, artikel ini cocok untuk Anda.  
Jika sering bertukar dokumen, Anda tentu pernah, atau mungkin sering mendapati bahwasanya dokumen yang tampak rapi di komputer kita, ternyata ditampilkan kacau balau di komputer lain.

Beberapa hal yang membuat tampilan dokumen berbeda antar komputer di antaranya:
- Beda piranti lunak pembuatnya dan penampilnya.  
  Berkas `.doc` yang dibuat menggunakan `LibreOffice` tidak akan persis sama dengan hasil dari `Microsoft Office` (MSO). Mungkin untuk dokumen sederhana tidak akan tampak bedanya, namun untuk dokumen yang lebih rumit, perbedaannya pasti kasat mata. Hal ini karena format `.doc` bukan format terbuka dan spesifikasinya hanya diketahui Microsoft, kemampuan `LibreOffice` untuk menyimpan dalam format `.doc` bisa dianggap sebagai _hack_.
  
- _Font_ (huruf)  
  Huruf sangat menentukan tampilan sebuah dokumen. Ketersediaan huruf dalam sebuah komputer dipengaruhi banyak hal, misalnya sistem operasi, piranti lunak yang terpasang, dan bahkan si pengguna. Jika komputer si pembaca dokumen tidak memiliki huruf yang digunakan oleh si pembuat dokumen, maka tampilannya akan berbeda. Perbedaan ini bisa semata kosmetik, misal huruf lebih tebal atau kurus dan paragraf lebih panjang. Bahkan fundamental seperti tidak tampilnya karakter tertentu dalam rumus matematika atau bahasa yang menggunakan huruf non latin.  
  
Nah, artikel ini hanya akan memberi solusi pada permasalahan huruf.  
Seperti disinggung sebelumnya, ketersediaan huruf dalam sebuah komputer di antaranya bergantung pada:
- Sistem operasi  
  Huruf dalam sebuah sistem operasi komersial tidak dilisensikan bebas pakai. Karenanya pengguna Linux tidak bisa begitu saja menggunakan huruf dari Microsoft Windows, atau Mac. Jadi beberapa huruf hanya eksklusif ada dalam sistem operasi tersebut, dan tidak boleh digunakan di sistem operasi lain.
  
  Di masa kejayaan Internet Explorer, ada rintisan agar tampilan situs web bisa seragam, yakni melalui proyek [_Core Fonts_](https://en.wikipedia.org/wiki/Core_fonts_for_the_Web). _Core fonts_ ini bisa juga digunakan dalam dokumen. Sayangnya inisiatif ini tidak berlanjut, dan pengembang situs web lebih memilih [_web fonts_](https://en.wikipedia.org/wiki/Web_font).
  
- Piranti lunak  
  Jika Anda menggunakan Windows dan memasang Microsoft Office (MSO), piranti lunak ini akan menambahkan pula beberapa huruf baru. Ini akan menambah kerumitan soal huruf, karena walaupun Anda menggunakan Windows namun tidak memasang MSO maka berkemungkinan untuk tidak dapat menampilkan dokumen yang dibuat menggunakan MSO dengan sempurna.  
  
- Pengguna komputer  
  Sebuah dokumen tidak cukup hanya bisa dibaca. Masalah tipografi dan estetika dokumen juga penting. Seringkali kita tidak memiliki huruf yang dapat menyajikan tampilan yang diinginkan, akhirnya kita pun mengunduh huruf baru dari internet.
  
Semua permasalahan huruf di atas dapat diselesaikan jika kita menyertakan huruf (_font embedding_) ke dalam dokumen yang kita buat. _Well, font embedding_ ini sebenarnya memperkenalkan sebuah masalah baru; penyebaran _licensed fonts_ yang tidak sesuai dengan lisensinya. Dengan kata lain, pembajakan. 

Berbeda dengan ulasan panjang lebar di atas, menyertakan huruf ke dalam dokumen yang dibuat menggunakan LibreOffice sangatlah singkat dan mudah. Berikut caranya.
- Sebelum menyimpan berkas, pilih menu **`File`** - **`Properties...`**, lalu klik bilah **`Font`** dan centang **`Embed fonts in the document`**. Klik tombol **`OK`** dan simpan dokumen seperti biasa.
  
_That's it?_  
_Yep, that's it!_

Berkas yang memiliki _embedded font_ akan berukuran lebih besar dibanding berkas biasa, namun akan lebih menjamin tampilan yang sama di tiap komputer.

