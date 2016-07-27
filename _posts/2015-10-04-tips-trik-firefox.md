---
layout: post
title: Tips dan Trik Firefox
tags: [firefox, iceweasel]
comments: true
---

*Browser* modern ibarat sistem operasi tersendiri, membutuhkan sumber daya besar dan memiliki banyak fitur. Artikel ini merupakan *tips* dan trik yang disarikan dari [Some useful Firefox tips to fix choppy scrolling and reduce memory usage](http://redd.it/39q6xt) untuk menyamankan penggunaaan Firefox dalam komputasi sehari-hari.

Jalankan `Firefox` kemudian ketikkan `about:config` di *address bar*, dan ...

### Perbaiki *scrolling* yang tersendat

Cari `layout.frame_rate.precise` dan tetapkan menjadi `enable`. Langkah ini akan meningkatkan kecepatan animasi.


### Mempercepat *loading* halaman

Cari `privacy.trackingprotection.enabled` dan tetapkan ke `true`. *Entry* ini adalah perlindungan *tracking* bawaan `Firefox`, yang selain melindungi pengguna juga meningkatkan kecepatan pemuatan halaman sekitar 20-40%. `privacy.trackingprotection.enabled` memiliki fungsi sama dengan *addons* `Disconnect`. Jadi jika langkah ini diterapkan, Anda dapat melepas *extensions* `Disconnect`.

 
### Menekan penggunaan *memory*

1. Ketika kita membuka sebuah laman yang banyak mengandung gambar, `Firefox` menyimpan gambar-gambar tersebut untuk sementara waktu dalam bentuk tidak termampatkan. Hal ini dimaksudkan agar `Firefox` dapat mengakses gambar-gambar tersebut secara cepat, namun sayangnya dengan mengorbankan penggunaan *memory*. Jika kita banyak membuka laman yang mengandung gambar-gambar berukuran besar, dan `Firefox` menyimpan gambar-gambar tersebut tanpa dimampatkan dan secara terus-menerus, maka akan menyebabkan penggunaan *memory* yang sangat besar. 
Masalah ini dapat diredam dengan menentukan batasan penggunaan *memory* untuk menyimpan gambar; cari `image.mem.max_decoded_image_kb` dan rendahkan nilai dari bawaannya yang sekitar `256000` atau `~256 MB` ke kisaran `51200` atau `~50 MB`.

2. *Memory* yang dianggarkan bagi Javascript terlalu tinggi. Pada laman web yang banyak mengandung javascript, penggunaan *memory* dapat meningkat dengan cepat. Untuk menekan penggunaan *memory* oleh Javascript, cari parameter `javascript.options.mem.max` dan rubah nilainya dari semula `-1` (otomatis) ke `51200` (50 MB).

3. Juga cari `javascript.options.mem.high_water_mark`, dan tetapkan ke `30` dari asalinya `128`. Parameter ini akan memerintahkan `garbage collector` untuk mulai bekerja ketika javascript menggunakan *memory* 30 MB. *Garbage collection* melepas *memory* kembali ke sistem agar bisa digunakan untuk hal lain.

4. `Firefox` menyimpan tiap *tab* yang terbuka sedemikian sehingga *tab* tersebut tidak perlu dibaca ulang ketika kita mengaksesnya melalui tombol *back* dan atau *forward*. Penyimpanan ini berbeda dengan *memory cache*. Asalinya nilai ditetapkan `50`, yang artinya `Firefox` akan mengingat 50 laman terakhir yang dibuka oleh tiap *tab*. Jika kita terbiasa membuka ulang *tab* (alih-alih membuka *tab* baru), ukuran riwayat *browser session* akan membengkak dengan sangat. Jadi apa yang mesti kita lakukan? Tutup *tab* jika telah selesai pada laman yang kita kunjungi dan buka *tab* baru untuk laman baru. Atau jalan lain, kita dapat menekan jumlah halaman yang disimpan `Firefox` dengan cara; buka `about:config` dan tetapkan nilai yang diinginkan di `browser.sessionhistory.max_entries`, misalnya di angka `10`.

 
### *Mouse scroll*

Untuk meningkatkan kisaran *scroll*, cari `mousewheel.min_line_scroll_amount` dan tetapkan ke nilai yang dirasa sesuai, misalnya `40`.

 
### Google Safeboat

Dimulai `Firefox` 32, Mozilla akan memeriksa berkas yang diunduh berdasarkan *application reputation database* dari Google.

*Catatan: `Firefox` hanya akan memeriksa berkas executable.*

Berkas unduhan akan diperiksa berdasar *database* lokal atau *database* dari Google jika *database* lokal tidak ada atau tidak mendapatkan hasil. Dalam mengakses *database* dari Google, informasi mengenai unduhan ikut disetor ke Google.

Meski berkas unduhan itu sendiri tidak ikut dikirim ke Google, sidik jari berkas tersebut dalam bentuk `SHA-256` dan informasi lain semisal nama berkas, ukuran dan dari mana berkas diunduh akan ikut dikirim.

Proses pemeriksaan tersebut dirasa tidak diperlukan bagi pengguna Linux, jadi kita dapat mematikannya:

* Ketik `about:preferences` dalam *address bar*.
* Ganti ke `Security` di bilah samping kiri.
* `Disable` entri `Block reported attack sites` dan `Block reported web forgeries`.

 
### Off Main Thread Compositing (OMTC)

Tujuan utama OMTC adalah untuk menggegaskan `Firefox`. Fitur ini memiliki beberapa kelebihan dibanding pendekatan `on-the-main-thread`, diantaranya:

* Menekan *main thread contention*
* *Asynchronous scrolling*
* Mencegah *tearing*
* *Asynchronous video*
* *Asynchronous CSS animation*

Fitur ini diterapkan pada semua *platform* kecuali Linux. Rujuk [Wiki Mozilla](https://wiki.mozilla.org/Platform/GFX/OffMainThreadCompositing) dan [Blog BenWa](https://benoitgirard.wordpress.com/2012/05/15/off-main-thread-compositing-omtc-and-why-it-matters/).

Menerapkan OMTC mungkin tidak akan berhasil dalam sistem Anda, namun tidak ada salahnya mencoba

Sebelum memulai, periksa apakag *GPU acceleration* telah berjalan.

* Buka `about:support`
* Cari bagian *Graphics*
* Periksa baris *GPU Accelerated Windows*

Jika nilainya `1/1` atau `2/2` atau lainnya, bagaimanapun, angka pertama mesti lebih besar dari `0`, maka `Firefox` teleh bekerja dengan *hardware accelerated*. Dan Anda tidak perlu mengikuti prosedur menghidupkan *hardware accelerated* berikut. Jika ternyata angka pertama adalah `0`, maka kita mesti mengaktifkan *hardware accelerated* sebagaimana berikut:

* Buka `about:config`
* Tetapkan `webgl.force-enabled` menjadi `true`. Ini akan memaksa `WebGL` aktif.
* Tetapkan `layers.acceleration.force-enabled` ke `true`. Ini akan memaksa *Layers Acceleration* aktif.
* Tetapkan `layers.offmainthreadcomposition.enabled` ke `true`. Ini akan menghidupkan *Off Main Thread Composition (OMTC)*, yang berakibat pada *composition* yang lebih cepat dan halus.

Jika kita ingin menerapkan lebih banyak fitur untuk menguji OMTC, kita dapat menetapkan `layers.offmainthreadcomposition.async-animations` ke `true`. *Properties* lain semisal `layers.async-video.enabled` mestinya sudah `true` secara asali. Karenanya, kita tidak perlu mengutak-atik nilai lain.

Juga jangan lupa untuk menghidupkan `Use hardware acceleration when available` di bilah `Advanced > General` dalam `about:preferences`.

Sebelum menetapkan pengaturan OMTC bagi seluruh sistem, periksa apakah `Firefox` berjalan lancar dengan `GPU acceleration`.

* Tutup seluruh proses`Firefox`.
* Jalankan `export MOZ_USE_OMTC=1 && firefox` dalam terminal.

Sekarang buka `about:support` dan periksa apakah  *GPU acceleration* telah hidup. Dan periksa apakah `Firefox` berjalan lancar tanpa kendala. Bagian `GPU Accelerated Windows` mestinya menunjukkan nilai semisal `1/1 OpenGL (OMTC)`.

Jika Anda pusa dengan hasilnya, mari tetapkan `environment variable` ini secara menyeluruh. Langkah berikut untuk Debian. Distribusi lain mungkin berbeda.

Buka berkas `/etc/environment` dan tambahkan baris berikut:

{% highlight sh %}
#force GPU acceleration for firefox
export MOZ_USE_OMTC=1
{% endhighlight %}

Hidupkan ulang komputer, dan jalankan `Firefox` seperti biasanya.


### Matikan penampil PDF

Buka `about:config` dan tetapkan `pdfjs.disabled` ke `true`.


### Matikan Firefox Hello

Buka `about:config` dan tetapkan `loop.enabled` ke `false`.
