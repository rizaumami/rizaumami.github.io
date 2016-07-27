---
layout: post
title: Membuat USB Installer Menggunakan easy2boot
tags: [install, windows, usb]
comments: true
---

Ada banyak *utility* untuk membuat *installer* sistem operasi komputer dari *flash disk* (UFD), satu yang lama saya gunakan adalah [Yumi](http://www.pendrivelinux.com/yumi-multiboot-usb-creator/). 

Namun kini saya menemukan sebuah *utility* UFD *installer* yang lebih mudah digunakan; [easy2boot](http://www.easy2boot.com/). Rujuk [http://www.easy2boot.com/](http://www.easy2boot.com/) untuk membandingkan keunggulan dan kekurangan easy2boot dibanding UFD *installer* lain.

Salah satu kelemahan utama dari UFD *installer* adalah ia umumnya berdasar grub4dos yang tidak bisa dijalankan dalam GNU/Linux. easy2boot juga hanya akan bekerja jika susunan berkas ISO *contiguous* (rapat) yang tidak mungkin dicapai jika kita menyalin berkas ISO dalam Linux, karena proses tulis Linux tidak meletakan berkas secara berdempetan.

Jadi, gunakan Windows untuk melakukan pemasangan easy2boot ke UFD. Berikut cara memasang easy2boot sebagaimana juga tertera di laman [http://www.easy2boot.com/make-an-easy2boot-usb-drive/](http://www.easy2boot.com/make-an-easy2boot-usb-drive/):

1. Unduh easy2boot di [http://www.easy2boot.com/download/](http://www.easy2boot.com/download/), disarankan untuk mengunduh veri DPMS.

2. Uraikan easy2boot ke `root` *partition* atau `PATH` yang tidak mengandung spasi, misal ke `C:\`

3. Masukkan UFD ke *port* USB

4. Gunakan `Windows Explorer` untuk masuk ke dalam map hasil uraian, kemudian **klik kanan** berkas `Make_E2B_USB_Drive (run as admin).cmd` dan pilih `Run as Administrator...`. Ikuti wisaya yang tampil.

5. Ketik **nomor** UFD dan pilih `FAT32`, atau `NTFS` jika ingin menyimpan berkas lebih besar dari 4GB ke dalam UFD.

6. `Make_E2B_USB_Drive.cmd` akan meminta Anda untuk menentukan bahasa, *keyboard* dan *menu option*, dan akan menyimpannya dalam berkas `_ISO\MyE2B.cfg`. Jika Anda tidak menginginkannya, ketik **S** untuk melewati langkah tersebut.

7. Selesai, UFD easy2boot siap digunakan.

Salin *payload* (berkas ISO) ke dalam map masing-masing, misal *installer* Windows 7 ke dalam map `_ISO\WINDOWS\WIN7` dan *Live CD* Ubuntu ke `_ISO\LINUX`.

Jika kapasitas UFD yang digunakan adalah terbatas, ada baiknya untuk menghapus berkas `_ISO\CONTIG.ISO` yang berukuran 500MB. `CONTIG.ISO` adalah *virtual drive* yang akan digunakan sebagai *temporary storage* bagi *payload* di bawah 500MB. Penggunaan `CONTIG.ISO` membuat *payload* tidak mesti *contiguous* sehingga tidak perlu *defrag*, jika `CONTIG.ISO` dihapus, pastikan untuk selalu defrag *payload* menggunakan `WinContig`.

## Memutakhirkan easy2boot

Unduh versi baru dan uraikan ke map kosong di `PATH` tanpa spasi, kemudian **klik kanan** berkas `UPDATE_E2B_DRIVE.CMD` dan pilih `Run as Administrator...`
