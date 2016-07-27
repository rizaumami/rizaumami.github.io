---
layout: post
title: Menghemat Daya ThinkPad T520 di Debian Sid Menggunakan Powertop
tags: [debian, thinkpad]
comments: true
---

Telah menjadi rahasia umum bahwasanya *power management* di GNU/Linux umumnya lebih buruk dibanding Windows. Ini dikarenakan *vendor* perangkat keras jarang yang mendukung sistem operasi minoritas seperti GNU/Linux.

Intel adalah satu diantara segelintir pabrikan yang memberikan layanan bagi produknya di dunia GNU/Linux. Artikel ini akan menjelaskan bagaimana menggunakan [powertop](https://01.org/powertop/), sebuah aplikasi *powersaving* dari Intel, untuk menghemat penggunaan daya ThinkPad T520 yang menggunakan sistem operasi Debian Sid.

* Pasang powertop menggunakan perintah `sudo aptitude install powertop`.
* Untuk mencegah pengukuran yang keliru, kita lakukan kalibrasi `powertop` dengan cara; isi baterai laptop hingga cukup untuk sekitar 30 menit, kemudian jalankan perintah `sudo powertop --calibrate` dan diamkan hingga proses kalibrasi selesai. Proses kalibrasi baiknya dilakukan 2-3 kali dengan *restart* laptop di antara proses kalibrasi.

Alih-alih menggunakan `sudo powertop --auto-tune` yang sering bermasalah dengan perangkat keras yang sering digunakan, misal *wireless mouse*, saya akan membuat `udev` *rules* dan `bash` *script* untuk menentukan kapan penghematan diperlukan dan perangkat apa saja yang perlu dikecualikan.

Cara kerja *script* ini adalah sebagai berikut; `udev` mendeteksi apakah laptop terhubung ke listrik PLN ataukah ditenagai baterai, jika terhubung listrik PLN maka jalankan `bash` script untuk menerapkan *power management* ke *performance*, dan jika laptop ditenagai baterai maka jalankan `bash` script untuk menerapkan *power management* ke *power saving*.

* Buat berkas `udev` *rules* menggunakan perintah `sudo nano /lib/udev/rules.d/96-t520powersave.rules` dan isikan *string* berikut:

{% highlight sh %}
# Jalankan powersaving script berdasar catu daya yang sedang digunakan
SUBSYSTEM=="power_supply", ENV{POWER_SUPPLY_ONLINE}=="0", RUN+="/usr/bin/t520powersave true"
SUBSYSTEM=="power_supply", ENV{POWER_SUPPLY_ONLINE}=="1", RUN+="/usr/bin/t520powersave false"
{% endhighlight %}

Bagaimana `bash` *script* tersebut menghemat daya? Dengan menerapkan parameter tertentu pada perangkat tertentu sebagaimana saran dari `powertop`.
Gunakan perintah `sudo powertop --csv` untuk menyimpan hasil pengukuran ke dalam berkas `powertop.csv`.

* Buat `bash` *script* menggunakan perintah `sudo nano /usr/bin/t520powersave`.
* Buka berkas `powertop.csv` menggunakan aplikasi *spreadheet* semisal LibreOffice Calc dan cari baris `*  *  *   Software Settings in Need of Tuning   *  *  *` kemudian salin kolom `script` ke dalam berkas `/usr/bin/t520powersave`, misal seperti berikut:

{% highlight sh %}
#!/bin/sh

case "$1" in
    true) # Enable power saving settings on battery
        echo 'min_power' > '/sys/class/scsi_host/host0/link_power_management_policy'
        echo 'min_power' > '/sys/class/scsi_host/host1/link_power_management_policy'
        echo 'min_power' > '/sys/class/scsi_host/host4/link_power_management_policy'
        echo 'min_power' > '/sys/class/scsi_host/host5/link_power_management_policy'
        echo 'min_power' > '/sys/class/scsi_host/host2/link_power_management_policy'
        echo 'min_power' > '/sys/class/scsi_host/host3/link_power_management_policy'
        echo 'auto' > '/sys/bus/usb/devices/2-1.5/power/control'
        echo 'auto' > '/sys/bus/usb/devices/2-1.4/power/control'
        echo 'auto' > '/sys/bus/usb/devices/1-1.2/power/control'
        echo 'auto' > '/sys/bus/usb/devices/1-1.1/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:0d:00.3/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:0d:00.0/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:03:00.0/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:1f.3/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:1f.2/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:00.0/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:02.0/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:16.0/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:1c.4/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:1f.0/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:1b.0/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:1a.0/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:1c.0/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:19.0/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:16.3/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:1c.1/power/control'
        echo 'auto' > '/sys/bus/pci/devices/0000:00:1d.0/power/control'
        ethtool -s enp0s25 wol d
	;;
    false) # Return to default on AC power
        echo 'max_performance' > '/sys/class/scsi_host/host0/link_power_management_policy';
        echo '3000' > '/proc/sys/vm/dirty_writeback_centisecs';
        for i in /sys/class/scsi_host/host*/link_power_management_policy; do echo max_performance > $i; done
        echo 'on' > '/sys/bus/usb/devices/2-1.5/power/control';
        echo 'on' > '/sys/bus/usb/devices/2-1.4/power/control';
        #echo 'auto' > '/sys/bus/usb/devices/1-1.2/power/control';
        for i in /sys/bus/pci/devices/*/power/control; do echo on > $i; done
        #/sbin/ethtool -s enp0s25 wol d;
        ;;
esac
exit 0
{% endhighlight %}

* Jalankan perintah `sudo udevadm control --reload-rules` agar *rules* yang baru kita buat langsung diterapkan.
