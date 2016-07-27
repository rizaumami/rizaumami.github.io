---
layout: post
title: Cara Kilat Membuat VirtualBox VM menggunakan VBoxManage
tags: [windows, virtualbox]
comments: true
---

*The fool of me* dengan ceroboh menghapus *virtual machine* andalan. Maklumlah, sejak memakai SSD jadi rajin bersih-bersih `¯\_(ツ)_/¯`

Merasa capek juga jika harus meruntu membuat ulang VM, akhirnya buka-buka manual VirtualBox dan akhirnya membuat *script* untuk mengotomatisasi pembuatan VBox VM. *Mangga* diamati dan disesuaikan kebutuhan *dulur-dulur sadaya* `( ͡° ͜ʖ ͡°)`.
Disarankan untuk membaca manual VirtualBox dan artikel terdahulu mengenai [Install Windows 7 OEM di VirtualBox](http://rizaumami.github.io/2015/08/02/install-windows-oem-di-vbox/).

{% highlight sh %}
#!/bin/bash

# VARIABLES --------------------------------------------------------------------

VBOXVMS="$HOME/VMs/VirtualBox VMs"
ISO_DISC=$HOME/Software/Windows/ISO/Windows7_Ultimate_x86_SP1.iso

# Nama Virtual Machine. Cari menggunakan perintah VBoxManage list vms.
VM_NAME="Windows 7"

# Path where you want to store the SLIC.bin file
SLIC_PATH="$VBOXVMS/$VM_NAME/SLIC.bin"
CFG_PATH='VBoxInternal/Devices/pcbios/0/Config'

# MAIN -------------------------------------------------------------------------

# Path dimana vms akan diletakkan
VBoxManage setproperty machinefolder "$VBOXVMS"

VBoxManage createvm --name "$VM_NAME" --register

# Impor data BIOS, system, motherboard dan chassis agar Windows OEM dapat diaktivasi
VSETED="VBoxManage setextradata $(VBoxManage showvminfo "$VM_NAME" | awk '/^UUID/{print $2}')"

$VSETED $CFG_PATH/DmiBIOSVendor         "string:$(sudo dmidecode -s bios-vendor)"
$VSETED $CFG_PATH/DmiBIOSVersion        "string:$(sudo dmidecode -s bios-version)"
$VSETED $CFG_PATH/DmiBIOSReleaseDate    "string:$(sudo dmidecode -s bios-release-date)"
$VSETED $CFG_PATH/DmiBIOSReleaseMajor   "$(sudo dmidecode -s bios-version | awk -F'[(]' '{print substr($2,0,1)}')"
$VSETED $CFG_PATH/DmiBIOSReleaseMinor   "$(sudo dmidecode -s bios-version | awk -F'[(]' '{print substr($2,3,2)}')"
$VSETED $CFG_PATH/DmiBIOSFirmwareMajor  "$(sudo dmidecode -t 0 | awk -F': ' '/Firmware Revision/{print substr($2,0,1)}')"
$VSETED $CFG_PATH/DmiBIOSFirmwareMinor  "$(sudo dmidecode -t 0 | awk -F'.' '/Firmware Revision/{print $2}')"

$VSETED $CFG_PATH/DmiSystemVendor       "string:$(sudo dmidecode -s system-manufacturer)"
$VSETED $CFG_PATH/DmiSystemProduct      "string:$(sudo dmidecode -s system-product-name)"
$VSETED $CFG_PATH/DmiSystemVersion      "string:$(sudo dmidecode -s system-version)"
$VSETED $CFG_PATH/DmiSystemSerial       "string:$(sudo dmidecode -s system-serial-number)"
$VSETED $CFG_PATH/DmiSystemUuid         "string:$(sudo dmidecode -s system-uuid)"
$VSETED $CFG_PATH/DmiSystemSKU          "<EMPTY>"                     # "SKU Number" di "dmidecode -t1"
$VSETED $CFG_PATH/DmiSystemFamily       "string:$(sudo dmidecode -t 1 | awk -F': ' '/Family/{print $2}')"

$VSETED $CFG_PATH/DmiBoardVendor        "string:$(sudo dmidecode -s baseboard-manufacturer)"
$VSETED $CFG_PATH/DmiBoardProduct       "string:$(sudo dmidecode -s baseboard-product-name)"
$VSETED $CFG_PATH/DmiBoardVersion       "<EMPTY>"                     # "Version" di "dmidecode -t2"
$VSETED $CFG_PATH/DmiBoardSerial        "string:$(sudo dmidecode -s baseboard-serial-number)"

$VSETED $CFG_PATH/DmiChassisAssetTag    "<EMPTY>"                     # "Asset Tag" di "dmidecode -t3"
$VSETED $CFG_PATH/DmiChassisSerial      "string:$(sudo dmidecode -s chassis-serial-number)"
$VSETED $CFG_PATH/DmiChassisVendor      "string:$$(sudo dmidecode -s chassis-manufacturer)"
$VSETED $CFG_PATH/DmiChassisVersion     "<EMPTY>"                     # "Version" di "dmidecode -t3"

SYS_SLIC="/sys/firmware/acpi/tables/SLIC"
if [ -e "$SYS_SLIC" ]
then
  sudo dd if="$SYS_SLIC" of="$SLIC_PATH"
  $VSETED VBoxInternal/Devices/acpi/0/Config/CustomTable "$SLIC_PATH"
fi

# Mengatur VM
# Lihat manual Bab 8 mengenai VBoxManage modifyvm
VBoxManage modifyvm "$VM_NAME" \
--ostype Windows7  \
--memory 3072  \
--pagefusion off \
--vram 256 \
--acpi on \
--ioapic on \
--hpet on \
--hwvirtex on \
--nestedpaging on \
--largepages off \
--vtxvpid on \
--vtxux on \
--pae on \
--longmode off \
--synthcpu off \
--cpus 2 \
--cpuhotplug off \
--cpuexecutioncap 100 \
--rtcuseutc off \
--monitorcount 1 \
--accelerate3d on \
--accelerate2dvideo on \
--firmware bios \
--chipset ich9 \
--boot1 disk \
--boot2 dvd \
--nic1 nat \
--nictype1 82540EM \
--cableconnected1 on \
--natpf1 Dapodikdas,tcp,127.0.0.1,8080,10.0.2.15,80 \
--macaddress1 auto \
--mouse usb \
--keyboard usb \
--audio pulse \
--audiocontroller hda \
--clipboard bidirectional \
--draganddrop bidirectional \
--usb on \
--usbehci on \
--snapshotfolder default \
--usbcardreader off \
--defaultfrontend default

VBoxManage sharedfolder add "$VM_NAME" --name "$USER" --hostpath "$HOME" --automount

# Membuat VBox disk
sudo VBoxManage createhd --filename "$VBOXVMS/$VM_NAME/windows7.vdi" --size 25600 --format VDI --variant Standard

sudo chown "$USER:$USER" "$VBOXVMS/$VM_NAME/"*

# Membuat media penyimpanan
VBoxManage storagectl "$VM_NAME" --name SATA --add sata --controller IntelAhci --hostiocache off --bootable on
# Mengaitkan disk ke media penyimpanan
VBoxManage storageattach "$VM_NAME" --storagectl SATA --port 0 --device 0 --type hdd --medium "$VBOXVMS/$VM_NAME/windows7.vdi" --nonrotational on
# Mengaitkan cakram ke media penyimpanan
VBoxManage storageattach "$VM_NAME" --storagectl SATA --port 1 --device 0 --type dvddrive --medium "$ISO_DISC"
{% endhighlight %}
