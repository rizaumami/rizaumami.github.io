---
layout: post
title: Memahami Konfigurasi TLP
tags: [tlp, thinkpad]
comments: true
---

Telah menjadi maklum bagi khalayak umum bahwasanya menggunakan GNU/Linux dalam laptop adalah bagaikan berjudi. Jika beruntung, semua perangkat keras langsung dikenali dan sistem berjalan dengan lancar, namun sebaliknya, sistem Linux akan selalu dirongrong dengan masalah.  
Salah satu masalah pelik yang dihadapi pengguna Linux dalam laptop adalah perihal penataan daya (_power management_). Pengguna komputer desktop tidak akan terlalu peduli soal penggunaan daya karena komputernya selalu terhubung ke sumber daya listrik, sementara laptop sumber dayanya hanya bersandar pada beberapa sel baterai.  

Mengapa dukungan penataan daya di GNU/Linux itu lemah? Tidak lain karena rendahnya dukungan _vendor_ perangkat keras. Umumnya _vendor_ perangkat keras hanya membuat _driver_ bagi sistem operasi Microsoft Windows, karena ialah pangsa pasar terbesar sistem operasi. Pangsa pengguna Linux yang kecil tidak menimbulkan minat para pembuat perangkat keras untuk membuatkan _driver_-nya. Akhirnya ini menjadi seperti lelucon ayam dan telur. Pembuat perangkat keras enggan melirik Linux karena penggunanya sedikit, dan jarang yang bersedia menggunakan Linux karena kurangnya dukungan untuk perangkat keras yang mereka miliki.

Untunglah pengguna GNU/Linux umumnya mau menggaruk kegatalannya sendiri. Awalnya, dukungan penataan daya di laptop tersedia dalam [_kernel laptop mode_](http://www.mjmwired.net/kernel/Documentation/laptops/laptop-mode.txt), kemudian skrip yang mendayagunakan _kernel laptop mode_, dan akhirnya ada yang membuat piranti lunak yang lebih berdaya guna sekaligus lebih mudah digunakan.  
Saya pernah menulis artikel tentang [menghemat daya ThinkPad T520 di Debian Sid dengan menggunakan powertop](/2015/07/09/menghemat-daya-thinkpad-t520-di-debian-sid-menggunakan-powertop/). Namun kini ada cara yang lebih mudah untuk menata daya di laptop, yakni dengan menggunakan [`TLP`](http://linrunner.de/en/tlp/tlp.html). Jika dulu Anda pernah mengenal [Jupiter](http://www.jupiterapplet.org/), pasti akan paham dengan manfaat [`TLP`](http://linrunner.de/en/tlp/tlp.html).

Memasang [`TLP`](http://linrunner.de/en/tlp/tlp.html) sangatlah mudah, jadi tidak akan dijabarkan dalam artikel kali ini. Dalam menggunakan [`TLP`](http://linrunner.de/en/tlp/tlp.html), kita saja pasang dan langsung pakai. Namun agar hasilnya lebih optimal sesuai perangkat keras yang kita pakai, adalah lebih baik untuk kemudian mengatur beberapa pengaturan di berkas `/etc/default/tlp`. Pengaturan di dalam berkas tersebut sekilas terlihat banyak dan membingungkan, namun jika kita menyempatkan untuk membacanya, tiap pengaturan selalu diberi sekilas petunjuk akan kegunaan pengaturan tersebut. Jika ternyata masih merasa bingung, silakan rujuk laman <http://linrunner.de/en/tlp/docs/tlp-configuration.html>.  

Jadi, kali ini saya akan mengupas pengaturan-pengaturan dalam berkas konfigurasi [`TLP`](http://linrunner.de/en/tlp/tlp.html) yang sesuai dengan ThinkPad T520 yang kini saya gunakan. Mari...

- ```bash
  TLP_ENABLE=1
  ```
  
  `TLP` tidak memiliki perintah _stop_ atau _restart_. Jadi bagaimana kalau kita sementara tidak ingin menggunakan `TLP`? Di sinilah pengaturannya; beri nilai `0` kemudian _reboot_.

- ```bash
  TLP_DEFAULT_MODE=AC
  ```
  
  Menentukan moda operasi _default_. Hanya bagi sistem sederhana semacam _embedded hardware_ yang seringkali tidak menyediakan metode deteksi catu daya apa yang kini sedang dipakai. Untuk sistem lengkap seperti laptop atau desktop pengaturan ini tidak diperlukan karena `TLP` bisa menentukannya secara otomatis. 
  
- ```bash
  DISK_IDLE_SECS_ON_AC=0
  DISK_IDLE_SECS_ON_BAT=2
  ```
  
  Nilai lebih besar dari 0 akan mengaktifkan _kernel laptop mode_, jadi tidak perlu untuk mengoprek `DISK_IDLE_SECS_ON_BAT` karena tujuan memasang `TLP` tentu salah satunya agar bisa memanfaatkan _kernel laptop mode_.
  
- ```bash
  MAX_LOST_WORK_SECS_ON_AC=15
  MAX_LOST_WORK_SECS_ON_BAT=60
  ```
  
  Ketika menulis sebuah berkas kemudian menyimpannya, kernel tidak serta-merta menuliskannya ke dalam diska, melainkan menyimpannya sementara ke dalam _Page cache_. Data-data yang sementara tersimpan dalam _Page cache_ ini dinamakan sebagai _dirty pages_. Pengaturan `MAX_LOST_WORK_SECS_ON` untuk menentukan berapa lama _dirty pages_ disimpan dalam _Page cache_. Semakin besar waktu ini, semakin besar pula resiko kehilangan data. Semakin kecil waktu ini, semakin kecil resiko kehilangan data, namun tentunya meningkatkan penggunaaan daya untuk menyapu _cache_ ke dalam diska.
  
- ```bash
  CPU_SCALING_GOVERNOR_ON_AC=powersave
  CPU_SCALING_GOVERNOR_ON_BAT=powersave
  ```
  
  Dalam menggunakan komputer, kita tidak selamanya malar berkerja menguras daya komputer, namun sering kali misalnya hanya santai mendengar musik atau membaca buku elektronik. menyikapi ini, CPU juga mengubah frekuensinya menyesuaikan beban kerja. Jika beban kerja meningkat, CPU akan meningkatkan frekuensinya, demikian sebaliknya. Kernel menggunakan _scalling governor_ untuk mengatur (_throttling_) frekuensi CPU ini.  
  `CPU_SCALING_GOVERNOR` akan menentukan _frequency scaling_ untuk _scalling governor_ dari CPU. Pengaturannya bergantung pada _driver_ yang digunakan sistem.  
  Gunakan perintah `sudo tlp-stat -p` untuk melihat _scaling driver_ apa yang digunakan sistem.  
  
  Prosesor ThinkPad T520 yang saya miliki adalah Intel Core i generasi kedua (Sandy Bridge), dan menurut `tlp-stat` menggunakan _driver_ `intel_pstate`. _Governor_ yang didukung untuk prosesor ini adalah `powersave` dan `performance`, namun `TLP` lebih menyarankan pengaturan pada `CPU_MIN/MAX_PERF`.
  
- ```bash
  CPU_MIN_PERF_ON_AC=0
  CPU_MAX_PERF_ON_AC=100
  CPU_MIN_PERF_ON_BAT=0
  CPU_MAX_PERF_ON_BAT=50
  ```
  
  Pengaturan _performance_ CPU ini yang lebih disarankan bagi prosesor yang menggunakan _driver_ `intel_pstate`.
  
- ```bash
  CPU_BOOST_ON_AC=1
  CPU_BOOST_ON_BAT=0 
  ```
  
  Pengaturan khusus untuk prosesor Intel Core i berkemampuan Turbo boost.
  
- ```bash
  SCHED_POWERSAVE_ON_AC=0
  SCHED_POWERSAVE_ON_BAT=1
  ```
  
  Prosesor modern memiliki kemampuan _hyperthreading_ yang seakan menyediakan kinerja setara lebih banyak inti (_multi core_) dibanding inti fisik yang dimiliki. `SCHED_POWERSAVE` akan meminimalkan penggunaan inti prosesor ketika bebannya rendah.
  
- ```bash
  ENERGY_PERF_POLICY_ON_AC=performance
  ENERGY_PERF_POLICY_ON_BAT=powersave 
  ```
  
  Menentukan garis besar kompromi antara penghematan daya dan kinerja CPU. Bagaimana agar kinerja CPU tinggi namun penggunaan dayanya rendah.  
  Pengaturan ini memerlukan modul kernel `msr` dan perkakas `x86_energy_perf_policy` yang sesuai dengan versi kernel.
  
- ```bash
   NMI_WATCHDOG=0 
  ```
  
  Sepertinya ini tidak terlalu penting bagi saya. Silakan baca [artikel Wikipedia mengenai NMI](https://en.wikipedia.org/wiki/Non-maskable_interrupt). 
  
- ```bash
   DISK_DEVICES="ata-PLEXTOR_PX-256M5S_P02302102450 ata-TOSHIBA_MQ01ABD100_X3TOP6YUT" 
  ```
  
  Pengaturan perangkat penyimpanan yang diinginkan untuk diatur oleh `TLP`. ThinkPad saya selain menggunakan SSD di _main bay_, juga menggunakan HDD di _ultra bay_ yang bisa dicopot-pasang dan ganti HDD, jadi penulisan sda, sdb, dan sejenisnya tidak memadai dan ada kemungkinan tertukar. Lebih baik untuk menggunakan UUID sebagai penanda HDD. Gunakan `tlp diskid` untuk mengetahui ID masing-masing diska. 
  
- ```bash
  DISK_APM_LEVEL_ON_AC="254 254"
  DISK_APM_LEVEL_ON_BAT="254 128"
  ```
  
  _Advanced Power Management Level_ bagi diska. Jika menggunakan AC, saya tetapkan APM ke 254 atau _maximum performance_. Jika laptop menggunakan baterai, saya tetapkan diska 1 yang SSD ke _max performance_ dan diska 2 yang HHD ke 128 sebagai kompromi penghematan daya dan kinerja. Diska 2 ini berupa HDD berisi data yang jarang diakses.
  
- ```bash
  DISK_SPINDOWN_TIMEOUT_ON_AC="0 120"
  DISK_SPINDOWN_TIMEOUT_ON_BAT="0 120"
  ```
  
  Menurunkan putaran piringan HDD jika sedang tidak digunakan, dalam satuan 5 detik. Karena diska 1 saya berupa SSD yang tidak memiliki komponen bergerak, tidak diperlukan pengaturan _spin down_. Nilai 120 atau 10 menit ditentukan untuk HDD di _ultra bay_ agar jika dalam 10 menit tidak digunakan ia akan menurunkan putaran kepingannya untuk menghemat daya.
  
- ```bash
   DISK_IOSCHED="noop cfq"
  ```
  
  I/O _scheduler_ adalah penjadwalan penulisan data dari _cache_ ke diska. Sarana penyimpanan data yang paling umum digunakan adalah _hard disk drive_ yang berupa piringan magnetik penyimpan data dan lengan penyimpan/pembaca data. Untuk menuliskan data, lengan penyimpan harus bergerak menuju sektor dalam piringan yang bisa ditulisi, dan ini membutuhkan waktu (_seek time_). Untuk meminimalkan akibat dari _seek time_ ini, maka diterapkan sebuah sistem penjadwalan penulisan dan pembacaan data, yakni I/O _scheduler_. Untuk SSD saya menggunakan [`noop`](https://en.wikipedia.org/wiki/Noop_scheduler) karena ia tidak memiliki komponen begerak dan data bisa langsung dituliskan, sementara untuk HDD saya menggunakan [`cfq`](https://en.wikipedia.org/wiki/Completely_Fair_Queuing).
  
- ```bash
  SATA_LINKPWR_ON_AC=max_performance
  SATA_LINKPWR_ON_BAT=max_performance
  ```
  
  Ini mengatur SATA _Agressive Link Power Management_ yang memungkinkan jalur SATA untuk berpindah ke moda _low-power_ kitika sedang tidak digunakan. Kerugiannya, ALPM menimbulkan jeda karena diska mesti diaktifkan ulang ketika hendak dipakai. Saya lebih memilih _max_performance_ karena lebih banyak menggunakan SSD yang sudah hemat daya. 
  
- ```bash
  AHCI_RUNTIME_PM_TIMEOUT=15
  ```
  
  [AHCI](https://en.wikipedia.org/wiki/Advanced_Host_Controller_Interface) masih berkaitan dengan jalur data ke diska. `AHCI_RUNTIME_PM` untuk mengatur tenggat waktu sebelum diska _suspended_.
  
- ```bash
  PCIE_ASPM_ON_AC=performance
  PCIE_ASPM_ON_BAT=powersave 
  ```
  
  Ini untuk mengatur [Active State Power Management](https://en.wikipedia.org/wiki/Active_State_Power_Management) dari perangkat [PCIe](https://en.wikipedia.org/wiki/PCI_Express). Sepertinya tidak ada perangkat PCIe terpasang di T520 ini, tapi tak apa lah...
  
- ```bash
  WIFI_PWR_ON_AC=off
  WIFI_PWR_ON_BAT=on
  ```
  
  Pengaturan hemat daya bagi perangkat _wrieless fidelity_. Tidak tahu juga apakah wi-fi T520 mendukung moda ini, tapi berhubung jarang menggunakan wi-fi kecuali jika _tethering_ ke telepon seluler, sepertinya tiada rugi untuk menggunakan pengaturannya ini.
  
- ```bash
  WOL_DISABLE=Y 
  ```
  
  _Wake on LAN_ adalah fungsi siaga komputer agar bisa dibangunkan ketika menerima perintah untuk bangun dari LAN. Ini cocoknya hanya untuk komputer perkantoran atau rumah yang memiliki jaringan. Jangankan _wake on LAN_, menggunakan LAN pun saya jarang.
  
- ```bash
  SOUND_POWER_SAVE_ON_AC=0
  SOUND_POWER_SAVE_ON_BAT=1
  ```
  
  Hemat daya untuk sistem audio Intel HDA. Ada kemungkinan menimbulkan gangguan "_clicking_" pada suara. 
  
- ```bash
  SOUND_POWER_SAVE_CONTROLLER=Y 
  ```
  Matikan daya _chip_ suara ketika tidak aktif. Khusus Intel HDA.
  
- ```bash
  BAY_POWEROFF_ON_BAT=1
  ```
  
  Putuskan daya ke _ultra bay_. Pengaturan hanya berpengaruh terhadap kandar optik. Jika _ultra bay_ diganti _caddy_ berisi diska, pengaturan ini tidak berpengaruh terhadap diska tersebut.
  
- ```bash
  BAY_DEVICE=sr0 
  ```
  PATH merujuk kandar optik, biasanya di `/dev/sr0`.
  
- ```bash
  RUNTIME_PM_ON_AC=on
  RUNTIME_PM_ON_BAT=auto
  ```
  
  Saya tidak terlalu paham soal _runtime power management_ untuk PCIe ini. Lagi pula sepertinya tidak punya perangkat yang memakai PCIe. 
  
- ```bash
  RUNTIME_PM_ALL=1
  ```
  
  Idem.
  
- ```bash
  USB_AUTOSUSPEND=1
  ```
  
  Putuskan daya ke perangkat USB jika dalam selang waktu tertentu tidak digunakan.
  
- ```bash
  USB_BLACKLIST="2717:ff60"
  ```
  
  Kecualikan perangkat tertentu agar dayanya jangan diputus (USB _autosuspend_) walau lama tidak aktif. Misal saya sering mengisi baterai telepon seluler di TP520 ini.  
  Cari ID menggunakan perintah `lsusb`. Jika ada banyak perangkat yang hendak _blacklisted_, pisahkan ID oleh spasi.
  
- ```bash
  USB_BLACKLIST_WWAN=1
  ```
  
  Ini khusus bagi pengguna modem _wireless wide area network_, agar modemnya tidak diputus daya walau tidak sedang digunakan. Kalau memang perlu mematikan _network_, T520 menyediakan sebuah saklar untuk mematikan perangkat radio secara fisik.
  
- ```bash
  RESTORE_DEVICE_STATE_ON_STARTUP=0
  ```
  
  Kadang saya langsung mematikan laptop begitu saja tanpa mematikan perangkat radionya terlebih dahulu. Dan jika laptop dihidupkan, yang saya inginkan hanyalah agar semua perangkat radio mati, tidak ada yang hidup dan berupaya terhubung ke internet.
  
- ```bash
   DEVICES_TO_DISABLE_ON_STARTUP="wifi wwan"
  ```
  
  _I don't want a surprise_. Sambungan internet terjalin jika saya memang menginginkannya. _No automated connection_.
  
- ```bash
  START_CHARGE_THRESH_BAT0=75
  STOP_CHARGE_THRESH_BAT0=80
  ```
  
  Ada banyak pro-kontra mengenai perlunya perlakuan _start/stop charging_ pada baterai. Sejujurnya saya juga tidak tahu banyak, kecuali bahwa _charge cycle_ baterai itu terbatas dan panas menurunkan mutu baterai.  
  Menjaga agar pengisian baterai tidak sampai penuh dan pemakaian tidak sampai tandas akan menjaga _charge cycle/cycle count_ tetap rendah. Demikian juga dengan tidak tetap mengisi baterai padahal telah penuh akan menurunkan suhu di dalam baterai (sepertinya laptop modern memiliki cara pemutus pengisian jika baterai telah penuh).  
  Mengabaikan silang pendapat, saya atur agar baterai mulai diisi jika kapasitasnya turun hingga 75% dan mulai diisi jika kapasitanya mencapai 80%. 
  
- ```bash
  START_CHARGE_THRESH_BAT1=75
  STOP_CHARGE_THRESH_BAT1=80
  ```
  
  Idem. Hanya saja ini untuk baterai tambahan (_slice battery_), yang kadang saya gunakan ketika tidak ada listrik PLN.
  
Demikian, sementara hanya itu. Mungkin ada pengaturan yang terlewat atau sengaja saya lewat baik karena tidak paham gunanya atau memang dirasa tidak berguna untuk ThinkPad T520 yang saya pakai.  
Jika artikel ini terasa membingungkan, jangan menyurutkan niat menggunakan `TLP`. _It's an amazing tool_. Pengaturan _default_ sudah cukup bagi penggunaan laptop secara umum.
