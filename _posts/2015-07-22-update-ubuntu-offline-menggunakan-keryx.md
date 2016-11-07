---
layout: post
title: Update Komputer Debian, Ubuntu, dan Turunannya Menggunakan Keryx
tag: [linux, keryx]
comments: true
---

Ketika sedang meramban isi HDD, tiba-tiba terantuk sebuah map bernama `Keryx`. Seketika ingatan dibawa mundur bertahun ke belakang. `Keryx` adalah *software* andalan saya ketika akses internet yang kencang hanyalah warnet; `Keryx` adalah alat bantu untuk memutakhirkan komputer bersistem operasi Debian, Ubuntu dan turunanannya yang tidak memiliki sambungan internet.

`Keryx` bekerja dengan cara:

1. Membuat *snapshot* dari komputer *offline*, yang dinamakan **project**. *Project* ini berisi daftar paket yang terpasang pada komputer dan *mirror* mana yang dipakai.

2. `Keryx` berisi *project* komputer *offline* dibawa ke komputer *online*. Di sini kita bisa melakukan *update* atau *installasi* paket selayaknya dalam komputer *offline*. 

3. `Keryx` yang telah melakukan *update* dan *install* di komputer *online* dibawa kembali ke komputer *offline* untuk memasang paket-paket yang telah diunduhnya.

Sayangnya pengembangan `Keryx` terhenti di usianya yang sangat belia. Selepas rilis versi 1.0 di 2011, pengembangannya terhenti. Malangnya, versi 1 ini tidak selengkap versi dibawahnya; v0.92.4, karena tidak memiliki berkas binari Windows. *Windows executable* sangat diperlukan untuk menjalankan `Keryx` di komputer warnet yang umumnya bersistem operasi Windows.

*Enough for the bad news*, setelah dicoba, ternyata `Keryx` masih bekerja dengan baik. Meski `apt` telah banyak berkembang sejak 2011, saya rasa `Keryx` masih dapat dijadikan andalan bagi mereka yang karena suatu hal sulit mendapatkan sambungan internet bagi komputer Debian, Ubuntu dan turunannya.

Berikut cara menggunakan `Keryx` disarikan dari tutorial yang terdapat dalam *tarball* `Keryx` v0.92.4.

* **Unduh Keryx**

  Unduh `Keryx` dari [https://launchpad.net/keryx/+download](https://launchpad.net/keryx/+download) atau dari repo github yang saya buat dengan cara:

{% highlight sh %}
git clone https://github.com/rizaumami/keryx.git
{% endhighlight %}

  atau dengan mengunduh `https://github.com/rizaumami/keryx/archive/master.zip`.

  Simpan dan uraikan ke dalam USB *flash disk*.

* **Menjalankan Keryx**

  **Dalam Linux**

  Jalankan berkas `source/keryx.py`.

  **Dalam Windows**

  Jalankan berkas `win32/keryx.exe`.

* **Membuat Project (di komputer offline)**

  Di awal membuka `Keryx`, masukkan nama *project* Anda, atau biarkan sesuai *default* dan klik tombol `New Project`.

  ![Membuat project](https://raw.githubusercontent.com/rizaumami/keryx/master/doc/Tutorial_Files/Create_Project.png)

  Ketika ditanya apakah Anda ingin mengunduh daftar paket terkini, pilih `No`, kemudian tutup `Keryx`.

* **Membuka Project (di komputer online)**

  Jalankan `Keryx` dan pilih *project* Anda dari menu *drop-down* paling bawah. Kemudian klik `Open Project`.

  ![Membuka project](https://raw.githubusercontent.com/rizaumami/keryx/master/doc/Tutorial_Files/Open_Project.png)

  Jika Anda ingin memiliki daftar paket terkini sesuai di *mirror/repo*, pilih `Yes` ketika ditanya apakah akan mengunduh daftar paket terkini (disarankan).

* **Memasang Paket (di komputer offline)**

  Buka `project` Anda dan pilih `Install Packages` dari menu `Project`. Periksa semua paket yang ingin Anda pasang dan klik `Continue`.

  ![Install Paket](https://raw.githubusercontent.com/rizaumami/keryx/master/doc/Tutorial_Files/Install_Packages.png)

  Sebuah `terminal` akan menampilkan laju pemasangan paket. Setelah pemasangan selesai, tekan **ENTER**.

  ![Install Paket](https://raw.githubusercontent.com/rizaumami/keryx/master/doc/Tutorial_Files/Installing_Packages.png)

Agar `Keryx` Menyadari bahwa Anda telah memiliki paket baru terpasang di komputer, Anda harus memutakhirkan *project status* (`Project` > `Update Status`).
