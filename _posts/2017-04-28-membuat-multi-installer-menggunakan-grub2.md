---
layout: post
title: Membuat Multi Installer Menggunakan GRUB2
tags: [grub, boot, iso]
comments: true
---

Jika sering berkeliling ke grup atau forum Linux, niscaya sering menemui pertanyaan senada berikut; "Bagaimana _burning_ ISO ke _flash disk_?", "_Burning_ ISO ke _flash disk_ pakai _software_ apa?", dan sejenisnya. _It's kind of boring, really...._ Meski memang bisa dimaklumi, tidak banyak piranti lunak GUI untuk membuat _installer_ USB _flash disk_ (UFD) di GNU/Linux. Sepertinya yang langsung terlintas di benak adalah `unetbootin`. Terlebih dengan tren ISO hibrida, banyak yang menyarankan penggunaan `dd` melalui terminal yang umumnya masih menjadi momok menyeramkan bagi pemula.

Uniknya, --atau mungkin lebih tepat, ironisnya-- piranti lunak pembuat UFD _installer_ ini lebih banyak diperuntukkan sistem operasi Windows, padahal banyak di antara piranti lunak tersebut mengandalkan _fork_ dari GRUB.  

Dari sekian banyak piranti lunak GUI pembuat UFD _installer_ di GNU/Linux, misal [`unetbootin`](https://github.com/unetbootin/unetbootin), [`gnome-multi-writer`](https://github.com/GNOME/gnome-multi-writer), atau [`etcher`](https://etcher.io/) sepertinya tidak ada yang benar-benar saya sukai. Umumnya piranti lunak ini merupakan _front end_ dari `dd` yang berarti mewajibkan semua isi UFD dibabad habis. 

Persyaratan utama yang saya ajukan bagi UFD _installer_ adalah ia mesti menjaga keutuhan isi UFD atau HDD _external_ yang digunakan, karena umumnya UFD atau HDD yang saya miliki telah berisi data. _Multiboot_ adalah nilai tambah, terlebih jika mampu _booting_ berkas ISO Windows.  
Selama ini saya menggunakan dua piranti lunak untuk membuat UFD _installer_; (1) menggunakan easy2boot sebagaimana pernah saya tulis di artikel "[Membuat USB Installer Menggunakan easy2boot](/2015/07/09/membuat-usb-installer-menggunakan-easy2boot/)", dan (2) menggunakan kemampuan GRUB2 untuk _booting_ berkas ISO sebagaimana telah saya tulis di artikel "[Boot ISO Menggunakan GRUB2](/2015/07/09/membuat-usb-installer-menggunakan-easy2boot/)".  

Artikel kali ini sebenarnya sama persis dengan artikel "[Boot ISO Menggunakan GRUB2](/2017/04/27/boot-iso-menggunakan-grub/)", hanya ditambahi cara memasang GRUB di UFD atau HDD _external_ yang dijadikan sarana penyimpanan _boot_. Sangat disarankan untuk membaca artikel tersebut terlebih dahulu.  
Langsung saja, berikut tutorial menjadikan UFD atau HDD _external_ sebagai _installer_ bagi banyak distro GNU/Linux. Sebagai bonus, data yang ada dalam UFD atau HDD _external_ akan tetap utuh dan _installer_ ini bisa pula _booting_ berkas ISO Windows _recovery_ atau PE selama ukurannya tidak melebihi 500MB.

- Tancapkan UFD atau HDD ke slot USB kemudian cari PATH-nya menggunakan `sudo lsblk -f`. Dimisalkan letaknya di `sda1` alias `/dev/sda1`.
- Kaitkan partisi tersebut, misal ke `/mnt`

  ```bash
  sudo mount /dev/sda1 /mnt
  ```
- Buat map untuk direktori GRUB dan menyimpan berkas-berkas ISO

  ```bash
  sudo mkdir /mnt/{boot,iso}
  ```
- Pasang GRUB ke _Master Boot Record_ (MBR) UFD atau HDD _external_. Karena tadi letak partisinya di `sda1`, maka MBR diska tersebut ada di `sda`.

  ```bash
  sudo grub-install --force --boot-directory=/mnt/boot /dev/sda
  ```
- Simpan berkas-berkas ISO yang hendak di-*boot* ke dalam map `/mnt/iso` dalam UFD/HDD.
- Buat berkas bernama `grub.cfg` dan simpan di `/mnt/boot/grub` UFD/HDD. Berikut dicontohkan menggunakan `nano`.

  ```bash
  sudo nano /mnt/boot/grub/grub.cfg
  ```
  
- Isi berkas `grub.cfg` tersebut dengan entri _boot_ dari masing-masing ISO yang terdapat dalam map `iso`. Kita bisa mencontoh entri _boot_ dari artikel [Boot ISO Menggunakan GRUB2](/2017/04/27/boot-iso-menggunakan-grub/) kemarin, misal seperti ini:

  ```bash
  menuentry "Arch 2015.10.01" --class iso {
    set isofile=/iso/archlinux-2015.10.01-dual.iso
    loopback loop $isofile
    linux (loop)/arch/boot/x86_64/vmlinuz archisolabel=ARCH_201508 img_dev=/dev/sda1 img_loop=$isofile earlymodules=loop
    initrd (loop)/arch/boot/x86_64/archiso.img
  }
  
  menuentry "Bodhi Linux 3.0 amd64" --class iso {
    set isofile=/iso/bodhi-3.0.0-64.iso
    loopback loop $isofile
    linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
    initrd (loop)/casper/initrd.lz
  }
  
  menuentry "elementary OS Freya amd64" --class iso {
    set isofile=/iso/elementaryos-stable-0.3.1-amd64.20150903.iso
    loopback loop $isofile
    linux (loop)/casper/vmlinuz boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
    initrd (loop)/casper/initrd.lz
  }
  
  menuentry "Fedora-Live-Workstation-x86_64-21-5" {
    set isofile=/iso/Fedora-Live-Workstation-x86_64-21-5.iso
    loopback loop $isofile
    linux (loop)/isolinux/vmlinuz0 iso-scan/filename=$isofile root=live:CDLABEL=Fedora-Live-WS-x86_64-21-5 rootfstype=auto ro rd.live.image quiet rhgb rd.luks=0 rd.md=0 rd.dm=0
    initrd (loop)/isolinux/initrd0.img
  }
  
  menuentry "Grml Rescue System (grml32-full_2014.11.iso)" {
    insmod part_msdos
    insmod btrfs
    set root='hd0,msdos1'
    if [ x$feature_platform_search_hint = xy ]; then
      search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1  44afbedc-1299-493f-b823-bd0b0ac7e61f
    else
      search --no-floppy --fs-uuid --set=root 44afbedc-1299-493f-b823-bd0b0ac7e61f
    fi
    iso_path="/iso/grml32-full_2014.11.iso"
    export iso_path
    kernelopts="   "
    export kernelopts
    loopback loop "/iso/grml32-full_2014.11.iso"
    set root=(loop)
    configfile /boot/grub/loopback.cfg
  }
  
  menuentry "Kubuntu 15.04 amd64" --class iso {
    set isofile=/iso/kubuntu-15.04-desktop-amd64.iso
    loopback loop $isofile
    linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
    initrd (loop)/casper/initrd.lz
  }
  
  menuentry "LinuxMint 17 Cinnamon amd64" {
   set isofile=/iso/linuxmint-17-cinnamon-dvd-64bit.iso
   loopback loop $isofile
   linux (loop)/casper/vmlinuz boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash --
   initrd (loop)/casper/initrd.lz
  }

  menuentry "LinuxMint 18 MATE amd64" {
    set isofile=/iso/linuxmint-18-mate-64bit.iso
    loopback loop $isofile
    linux (loop)/casper/vmlinuz boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash --
    initrd (loop)/casper/initrd.lz
  }
  
  menuentry "Lubuntu 15.04 amd64" --class iso {
    set isofile=/iso/lubuntu-15.04-desktop-amd64.iso
    loopback loop $isofile
    linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
    initrd (loop)/casper/initrd.lz
  }
  
  # Jika gagal boot, coba-coba ubah /dev/sdc1 ke diska yang sesuai, misal sda1, sda2, dst, atau sdb1, sdb2 dst
  menuentry "Manjaro LXQt 0.8.12 i686" --class iso {
    set isofile=/iso/manjaro-lxqt-0.8.12-i686.iso
    loopback loop $isofile
    linux (loop)/manjaro/boot/i686/manjaroiso misobasedir=manjaro misolabel=MJRO0812 img_dev=/dev/sdc1 img_loop=$isofile
    initrd (loop)/manjaro/boot/i686/manjaro.img
  }
  
  menuentry "Parted Magic 2015.01.13" {
    linux16 /boot/memdisk iso
    initrd16 /iso/pmagic_2015_01_13.iso
  }
  
  menuentry "Repair_disc_Windows_7_32-bit" {
    linux16 /boot/memdisk iso
    initrd16 /iso/Windows/Repair_disc_Windows_7_32-bit.iso
  }
  
  menuentry "Repair_disc_Windows_7_64-bit" {
    linux16 /boot/memdisk iso
    initrd16 /home/iza/Software/Windows/ISO/Repair_disc_Windows_7_64-bit.iso
  }
  
  menuentry "Siduction 14.1.0 LXQT amd64" --class iso {
    set isofile=/iso/siduction-14.1.0-paintitblack-lxqt-amd64-201405081704.iso
    loopback loop $isofile
    linux (loop)/boot/vmlinuz0.amd boot=boot iso-scan/filename=$isofile quiet noeject noprompt splash toram --
    initrd (loop)/boot/initrd0.amd
  }
  
  menuentry "Super Grub2 Disk Hybrid 2.02s3" {
    linux16 /boot/memdisk iso
    initrd16 /iso/super_grub2_disk_hybrid_2.02s3.iso
  }
  
  menuentry 'SolusOS 2017.04.18 Budgie' {
    set isofile='/iso/Solus-2017.04.18.0-Budgie.iso'
    loopback loop $isofile
    linux (loop)/boot/kernel boot=casper iso-scan/filename=$isofile noprompt noeject root=live:CDLABEL=SolusLiveBudgie ro rd.live.image rd.luks=0 rd.md=0 rd.dm=0 -- 
    initrd (loop)/boot/initrd.img
  }
  
  menuentry "SystemRescueCD 4.8.2-beta003 32bit" {
    set isofile=/iso/systemrescuecd-x86-4.8.2-beta003.iso
    loopback loop $isofile
    linux (loop)/isolinux/rescue32 setkmap=us isoloop=$isofile
    initrd (loop)/isolinux/initram.igz
  }
  
  menuentry "Ubuntu 16.04.1 amd64" --class iso {
    set isofile=/iso/ubuntu-16.04.1-desktop-amd64.iso
    loopback loop $isofile
    linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
    initrd (loop)/casper/initrd.lz
  }
  
  menuentry "Ubuntu 17.04 amd64" --class iso {
    set isofile=/iso/ubuntu-17.04-desktop-amd64.iso
    loopback loop $isofile
    linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
    initrd (loop)/casper/initrd.lz
  }
  
  menuentry "Ubuntu MATE 14.10 amd64" --class iso {
    set isofile=/iso/ubuntu-mate-14.10-desktop-amd64.iso
    loopback loop $isofile
    linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
    initrd (loop)/casper/initrd.lz
  }
  
  menuentry "Void Linux 4.5.2_1 x86_64 (RAM)" {
    set isofile=/iso/void-live-x86_64-20160420-lxqt.iso
    loopback loop $isofile
    linux (loop)/boot/vmlinuz iso-scan/filename=$isofile root=live:CDLABEL=VOID_LIVE init=/sbin/init ro rd.luks=0 rd.md=0 rd.dm=0 loglevel=4 vconsole.unicode=1 vconsole.keymap=us locale.LANG=en_US.UTF-8  rd.live.ram  
    initrd (loop)/boot/initrd
  }
  
  menuentry "Windows 7 PESE x86" {
     linux16 /boot/memdisk iso
     initrd16 /iso/Win7PESE_x86.iso
  }
  ```

  Simpan berkas dengan menekan `CTRL+O` kemudian keluar dari `nano` menggunakan `CTRL+X`. 

  Tentunya jika berkas ISO Anda tidak tercantum dalam contoh, berarti mesti mencari sendiri entri _boot_ yang tepat. Untuk Ubuntu dan turunannya biasanya tinggal menyesuaikan nama berkas ISO saja.
  
- Pasang paket `sysutils` kemudian salin `/usr/lib/syslinux/memdisk` ke `/mnt/boot` agar kita bisa _booting_ entri yang terdapat `memdisk` di dalam barisnya. 

  ```bash
  sudo aptitude install sysutils
  sudo cp /usr/lib/syslinux/memdisk /mnt/boot
  ```
  
  `memdisk` hanya bisa _booting_ ISO berukuran di bawah 500MB. FYI, `memdisk` di Grub4DOS bisa _booting_ berkas berukuran lebih dari 500MB, entah bagaimana caranya, mungkin telah _patched_.

- _That's it_. Saatnya untuk _reboot_ dan menjajal apakah _installer_ ini bekerja dengan baik.
