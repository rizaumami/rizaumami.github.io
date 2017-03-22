---
layout: post
title: Install Windows 7 OEM di VirtualBox
tags: [windows, virtualbox]
comments: true
---

Telah beberapa tahun ini saya menggunakan GNU/Linux sebagai sistem operasi utama di ThinkPad kesayangan.
Aslinya ThinkPad ini disertai Windows 7 Professional OEM, karenanya terkadang merasa sayang jika hanya menggunakan Linux, soalnya lisensi Windows kan tidak gratis.

But, *worry not*. Kita bisa menggunakan lisensi ini di VirtualBox (atau VM lainnya semisal VMWare). Hal ini legal karena Windows tidak dipasang selain di VBox di komputer ini.

Agar Windows OEM yang dipasang dalam VBox dapat diaktivasi maka VBox harus menyediakan lingkungan yang sama dengan komputer aslinya. Hal ini tidak memungkinkan karena VBox bukan *bare metal hypervisor*, karenanya kita akan menyediakan data BIOS, *Motherboard* dan *firmware* yang sama dengan ThinkPad aslinya sehingga membuat Windows mengira ia dipasang di fisik ThinkPad asli.

Berikut langkah agar dapat memasang Windows OEM di VirtualBox:

* Buat sebuah *Virtual Machine* baru. Jangan lekas dipasang Windows, biarkan saja. Lihat nama VM menggunakan perintah `VBoxManage list vms` kemudian catat `UUID`-nya.

* Jalankan `dmidecode` untuk mengetahui data:
  * BIOS      : `sudo dmidecode -t 0`
  * System    : `sudo dmidecode -t 1`
  * Baseboard : `sudo dmidecode -t 2`
  * Chassis   : `sudo dmidecode -t 3`

* Masukkan data dari `dmidecode` tersebut ke VBox menggunakan perintah `VBoxManage setextradata` sesuai manual VirtualBox bab 9. Untuk mudahnya, lihat contoh *script* berikut, sesuaikan `SLIC_PATH` dan nilai `DMI` yang didapat dari perintah `dmidecode` di atas:

  ```sh
  #! /bin/bash

  VM_NAME="5021b590-322e-4d02-8e91-b1937f5fc681"                        # Nama Virtual Machine. Cari menggunakan perintah VBoxManage list vms.  
  SLIC_PATH="$HOME/VMs/VirtualBox VMs/SLIC.bin"                         # Path dimana berkas SLIC.bin akan disimpan
  VSETED="VBoxManage setextradata $VM_NAME"
  CFG_PATH="VBoxInternal/Devices/pcbios/0/Config"

  $VSETED $CFG_PATH/DmiBIOSVendor         "string:LENOVO"               # "Vendor" di "dmidecode -t0"
  $VSETED $CFG_PATH/DmiBIOSVersion        "string:8AET63WW (1.43 )"     # "Version" di "dmidecode -t0"
  $VSETED $CFG_PATH/DmiBIOSReleaseDate    "string:05/08/2013"           # "Release Date" di "dmidecode -t0"
  $VSETED $CFG_PATH/DmiBIOSReleaseMajor   "1"                           # "Bios Revision (satuan)" di "dmidecode -t0"
  $VSETED $CFG_PATH/DmiBIOSReleaseMinor   "43"                          # "Bios Revision (di belakang koma/titik)" di "dmidecode -t0"
  $VSETED $CFG_PATH/DmiBIOSFirmwareMajor  "1"                           # "Firmware Revision (satuan)" di "dmidecode -t0"
  $VSETED $CFG_PATH/DmiBIOSFirmwareMinor  "36"                          # "Firmware Revision (di belakan koma/titik)" di "dmidecode -t0"

  $VSETED $CFG_PATH/DmiSystemVendor       "string:LENOVO"               # "Manufacturer" di "dmidecode -t1"
  $VSETED $CFG_PATH/DmiSystemProduct      "string:4243FG7"              # "Product Name" di "dmidecode -t1"
  $VSETED $CFG_PATH/DmiSystemVersion      "string:ThinkPad T520"        # "Version" di "dmidecode -t1"
  $VSETED $CFG_PATH/DmiSystemSerial       "string:Rxxxxx1"              # "Serial Number" di "dmidecode -t1"
  $VSETED $CFG_PATH/DmiSystemSKU          "<EMPTY>"                     # "SKU Number" di "dmidecode -t1"
  $VSETED $CFG_PATH/DmiSystemUuid         "string:527EB101-xxxx-xxxx-xxxx-D43D91159C5B"  # "UUID" di "dmidecode -t1"
  $VSETED $CFG_PATH/DmiSystemFamily       "string:ThinkPad T520"        # "Family" di "dmidecode -t1"

  $VSETED $CFG_PATH/DmiBoardVendor        "string:LENOVO"               # "Manufacturer" di "dmidecode -t2"
  $VSETED $CFG_PATH/DmiBoardProduct       "string:4243FG7"              # "Product Name" di "dmidecode -t2"
  $VSETED $CFG_PATH/DmiBoardVersion       "<EMPTY>"                     # "Version" di "dmidecode -t2"
  $VSETED $CFG_PATH/DmiBoardSerial        "string:1xxxxxxxxx6"          # "Serial Number" di "dmidecode -t2"

  $VSETED $CFG_PATH/DmiChassisAssetTag    "<EMPTY>"                     # "Asset Tag" di "dmidecode -t3"
  $VSETED $CFG_PATH/DmiChassisSerial      "string:Rxxxxx1"              # "Serial Number" di "dmidecode -t3"
  $VSETED $CFG_PATH/DmiChassisVendor      "string:LENOVO"               # "Manufacturer" di "dmidecode -t3"
  $VSETED $CFG_PATH/DmiChassisVersion     "<EMPTY>"                     # "Version" di "dmidecode -t3"

  SYS_SLIC="/sys/firmware/acpi/tables/SLIC"
  if [ -e "$SYS_SLIC" ]
  then
    sudo dd if="$SYS_SLIC" of="$SLIC_PATH"
    $VSETED VBoxInternal/Devices/acpi/0/Config/CustomTable "$SLIC_PATH"
  fi
  ```

Jalankan script kemudian install Windows seperti biasa. Jangan aktifkan Windows selama pemasangan, aktifkan setelah Windows terpasang di VBox.

Good luck `( ͡° ͜ʖ ͡°)`
