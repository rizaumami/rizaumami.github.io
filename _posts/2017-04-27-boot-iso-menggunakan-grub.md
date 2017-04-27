---
layout: post
title: Boot ISO Menggunakan GRUB2
tags: [iso, grub]
comments: true
---

Kebanyakan distro GNU/Linux menyebarluaskan media _installer_-nya dalam bentuk _bootable_ ISO yang menyediakan _live environment_.
Ini merupakan cara cerdas di mana si calon pengguna dapat menguji distro tersebut apakah cocok dengan perangkat keras yang dimilikinya, dengan kebutuhannya, dan tentu saja apakah cocok dengan seleranya, baru kemudian dia dapat memutuskan apakah layak untuk memasangnya.

Dasarnya, ISO adalah sebuah citra (_image_) dari sebuah kepingan cakram optik, entah CD, DVD, atau bahkan Blue Ray. Penyebutan ISO berasal dari format _file system_ [ISO 9660](https://en.wikipedia.org/wiki/ISO_9660) yang umumnya dipakai dalam media optik. Jadi sebuah berkas ISO dimaksudkan untuk dituliskan (istilahnya; dibakar -terjemahan dari bahasa Inggris; _burn_) ke dalam kepingan cakram optik.
Namun demikian, seiring tuntutan pasar banyak beredar komputer tanpa kandar optik demi menekan harga jual dan juga sesuai kenyataan di mana cakram optik kian tersisihkan oleh media penyimpanan baru; _USB flash disk_ (UFD).
Untungnya, kita tetap dapat menggunakan berkas ISO ini dan menggunakan UFD sebagai pengganti kepingan CD atau DVD dengan menggunakan aplikasi semacam `unetbootin`. Selain itu, pihak distro juga menyikapi kecenderungan pengguna yang lebih memilih menggunakan UFD dengan merilis berkas citra ISO hibrida, yang selain bisa _burned_ ke keping optik, juga bisa langsung disalin ke UFD dengan menggunakan `dd`, sebuah _tool_ yang dipastikan hampir ada di semua sistem GNU/Linux.

Sekarang, bagaimana jika kita tidak memiliki UFD di tangan? Entah karena dipinjam, rusak, kapasitasnya lebih kecil dari berkas ISO, atau alasan lainnya?
_Worry not_, Untuk beberapa distro, terutama Ubuntu dan turunannya, kita dapat langsung _booting_ dari berkas ISO yang tersimpan dalam HDD.
Syaratnya: Kita harus sudah memiliki installasi `GRUB2` atau GNU/Linux dalam HDD, jadi hanya cocok bagi mereka yang telah memiliki sistem Linux. Sebenarnya ada beberapa _bootloader_ lain yang bisa _booting_ langsung berkas ISO dari diska, misal GRUB, Grub4DOS, dan LiLo, namun trik berikut hanya untuk `GRUB2`.

Ada satu lagi alasan mengapa saya menyukai _boot_ ISO langsung dari diska, yakni beberapa ISO bisa langsung dimuat ke RAM. Perkawinan penyimpanan ISO di SSD dan _booted_ langsung ke RAM melahirkan kinerja yang luar biasa. _Well_, kelemahannya tentu saja Anda harus memiliki SSD dan RAM berkapasitas memadai.

Namun, tentu saja tiada yang sempurna tanpa cela. Meski di Debian telah ada paket `grub-imageboot` yang membuat kita cukup meletakkan berkas ISO di `/boot/images` dan kemudian melakukan `update-grub`, namun keberhasilannya terbatas pada distro-distro populer seperti Ubuntu dan turunannya. Jadi, saya seringkali dipaksa untuk melakukan _trial and error_ demi mendapatkan konfigurasi yang pas bagi ISO yang hendak _booted_.

Berikut beberapa pengaturan untuk ISO yang telah berhasil saya jalankan melalui GRUB2 langsung dari diska. Beberapa ISO mungkin sudah lawas, namun tetap bisa dijadikan panduan untuk membuat konfigurasi baru bagi ISO yang lebih anyar.

- Sunting berkas `/etc/grub.d/40_custom`.  
  Ini agar perubahan yang dilakukan tetap bertahan terhadap pemutakhiran `update-grub`.

  Isikan _boot entry_ yang akan digunakan untuk _booting_ berkas ISO. Entri akan beragam tergantung letak ISO dan nama ISO, juga mungkin pada distro tertentu membutuhkan penambahan parameter tertentu. Dalam contoh di bawah, berkas-berkas ISO disimpan di `/home/iza/Software/ISO/`.

  ```bash 
  submenu "Boot ISO dari disk" {
  
    menuentry "Arch 2015.10.01" --class iso {
      set isofile=/home/iza/Software/ISO/archlinux-2015.10.01-dual.iso
      loopback loop $isofile
      linux (loop)/arch/boot/x86_64/vmlinuz archisolabel=ARCH_201508 img_dev=/dev/sda1 img_loop=$isofile earlymodules=loop
      initrd (loop)/arch/boot/x86_64/archiso.img
    }
    
    menuentry "Bodhi Linux 3.0 amd64" --class iso {
      set isofile=/home/iza/Software/ISO/bodhi-3.0.0-64.iso
      loopback loop $isofile
      linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
      initrd (loop)/casper/initrd.lz
    }
    
    menuentry "elementary OS Freya amd64" --class iso {
      set isofile=/home/iza/Software/ISO/elementaryos-stable-0.3.1-amd64.20150903.iso
      loopback loop $isofile
      linux (loop)/casper/vmlinuz boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
      initrd (loop)/casper/initrd.lz
    }
    
    menuentry "Fedora-Live-Workstation-x86_64-21-5" {
      set isofile=/home/iza/Software/ISO/Fedora-Live-Workstation-x86_64-21-5.iso
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
            iso_path="/home/iza/Software/ISO/grml32-full_2014.11.iso"
            export iso_path
            kernelopts="   "
            export kernelopts
            loopback loop "/home/iza/Software/ISO/grml32-full_2014.11.iso"
            set root=(loop)
            configfile /boot/grub/loopback.cfg
    }
    
    menuentry "Kubuntu 15.04 amd64" --class iso {
      set isofile=/home/iza/Software/ISO/kubuntu-15.04-desktop-amd64.iso
      loopback loop $isofile
      linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
      initrd (loop)/casper/initrd.lz
    }
    
    menuentry "LinuxMint 17 Cinnamon amd64" {
     set isofile=/home/iza/Software/ISO/linuxmint-17-cinnamon-dvd-64bit.iso
     loopback loop $isofile
     linux (loop)/casper/vmlinuz boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash --
     initrd (loop)/casper/initrd.lz
    }

    menuentry "LinuxMint 18 MATE amd64" {
      set isofile=/home/iza/Software/ISO/linuxmint-18-mate-64bit.iso
      loopback loop $isofile
      linux (loop)/casper/vmlinuz boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash --
      initrd (loop)/casper/initrd.lz
    }
    
    menuentry "Lubuntu 15.04 amd64" --class iso {
      set isofile=/home/iza/Software/ISO/lubuntu-15.04-desktop-amd64.iso
      loopback loop $isofile
      linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
      initrd (loop)/casper/initrd.lz
    }
    
    # Jika gagal boot, coba-coba ubah /dev/sdc1 ke diska yang sesuai, misal sda1, sda2, dst, atau sdb1, sdb2 dst
    menuentry "Manjaro LXQt 0.8.12 i686" --class iso {
      set isofile=/home/iza/Software/ISO/manjaro-lxqt-0.8.12-i686.iso
      loopback loop $isofile
      linux (loop)/manjaro/boot/i686/manjaroiso misobasedir=manjaro misolabel=MJRO0812 img_dev=/dev/sdc1 img_loop=$isofile
      initrd (loop)/manjaro/boot/i686/manjaro.img
    }
    
    menuentry "Parted Magic 2015.01.13" {
      linux16 /boot/memdisk iso
      initrd16 /home/iza/Software/ISO/pmagic_2015_01_13.iso
    }
    
    menuentry "Repair_disc_Windows_7_32-bit" {
      linux16 /boot/memdisk iso
      initrd16 /home/iza/Software/ISO/Windows/Repair_disc_Windows_7_32-bit.iso
    }
    
    menuentry "Repair_disc_Windows_7_64-bit" {
      linux16 /boot/memdisk iso
      initrd16 /home/iza/Software/Windows/ISO/Repair_disc_Windows_7_64-bit.iso
    }
    
    menuentry "Siduction 14.1.0 LXQT amd64" --class iso {
      set isofile=/home/iza/Software/ISO/siduction-14.1.0-paintitblack-lxqt-amd64-201405081704.iso
      loopback loop $isofile
      linux (loop)/boot/vmlinuz0.amd boot=boot iso-scan/filename=$isofile quiet noeject noprompt splash toram --
      initrd (loop)/boot/initrd0.amd
    }
    
    menuentry "Super Grub2 Disk Hybrid 2.02s3" {
      linux16 /boot/memdisk iso
      initrd16 /home/iza/Software/ISO/super_grub2_disk_hybrid_2.02s3.iso
    }
    
    menuentry 'SolusOS 2017.04.18 Budgie' {
      set isofile='/home/iza/Software/ISO/Solus-2017.04.18.0-Budgie.iso'
      loopback loop $isofile
      linux (loop)/boot/kernel boot=casper iso-scan/filename=$isofile noprompt noeject root=live:CDLABEL=SolusLiveBudgie ro rd.live.image rd.luks=0 rd.md=0 rd.dm=0 -- 
      initrd (loop)/boot/initrd.img
    }
    
    menuentry "SystemRescueCD 4.8.2-beta003 32bit" {
      set isofile=/home/iza/Software/ISO/systemrescuecd-x86-4.8.2-beta003.iso
      loopback loop $isofile
      linux (loop)/isolinux/rescue32 setkmap=us isoloop=$isofile
      initrd (loop)/isolinux/initram.igz
    }
    
    menuentry "Ubuntu 16.04.1 amd64" --class iso {
      set isofile=/home/iza/Software/ISO/ubuntu-16.04.1-desktop-amd64.iso
      loopback loop $isofile
      linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
      initrd (loop)/casper/initrd.lz
    }
    
    menuentry "Ubuntu 17.04 amd64" --class iso {
      set isofile=/home/iza/Software/ISO/ubuntu-17.04-desktop-amd64.iso
      loopback loop $isofile
      linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
      initrd (loop)/casper/initrd.lz
    }
    
    menuentry "Ubuntu MATE 14.10 amd64" --class iso {
      set isofile=/home/iza/Software/ISO/ubuntu-mate-14.10-desktop-amd64.iso
      loopback loop $isofile
      linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile quiet noeject noprompt splash toram --
      initrd (loop)/casper/initrd.lz
    }
    
    menuentry "Void Linux 4.5.2_1 x86_64 (RAM)" {
      set isofile=/home/iza/Software/ISO/void-live-x86_64-20160420-lxqt.iso
      loopback loop $isofile
      linux (loop)/boot/vmlinuz iso-scan/filename=$isofile root=live:CDLABEL=VOID_LIVE init=/sbin/init ro rd.luks=0 rd.md=0 rd.dm=0 loglevel=4 vconsole.unicode=1 vconsole.keymap=us locale.LANG=en_US.UTF-8  rd.live.ram  
      initrd (loop)/boot/initrd
    }
    
    menuentry "Windows 7 PESE x86" {
       linux16 /boot/memdisk iso
       initrd16 /home/iza/Software/ISO/Win7PESE_x86.iso
    }
  }
  ```
  
  Untuk entri yang terdapat `memdisk` di dalamnya mengharuskan kita memasang paket `syslinux` dan ukuran berkas ISO tidak lebih dari 500MB agar bisa _booting_ entri tersebut. Salin `/usr/lib/syslinux/memdisk` ke `/boot`, atau rubah baris `/boot/memdisk` menjadi `/usr/lib/syslinux/memdisk`.
  
- Mutakhirkan GRUB agar membuat berkas `grub.cfg` yang baru dengan menambahkan pilihan untuk _boot_ dari ISO sesuai entri di atas.

  ```bash
  sudo update-grub
  ```
  
- Hidupkan ulang komputer dan jajal apakah ISO mampu di-*boot*.

