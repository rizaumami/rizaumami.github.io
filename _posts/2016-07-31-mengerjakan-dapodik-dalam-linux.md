---
layout: post
title: Mengerjakan Dapodik 2016 dalam Linux
tags: [dapodik, virtualbox, linux]
comments: true
---

Hari ini Dapodik versi 2016 telah diluncurkan.  
Jika boleh berpendapat, mestinya Dapodik kembali menggunakan _Software as a service_ layaknya Dapodikdas generasi awal atau [Padamu Negeri](http://padamu.siap.web.id/). Sebagai pengguna GNU/Linux, rada ribed juga karena walaupun Dapodik menggunakan aplikasi _open source_ macam Apache, PHP dan postgre SQL, namun sulit dan hampir tidak mungkin untuk dijalankan dalam Linux.  
Dan sepertinya Dapodik versi 2016 ini memiliki ketergantungan terhadap dot NET, _well shit_...

Entah mengapa saya tidak pernah merasa nyaman mengerjakan Dapodik secara langsung dalam VirtualBox, mungkin karena banyak melakukan _copy-paste_ antar dokumen atau memang sudah tidak terbiasa dengan lingkungan Windows.  
Mungkin ada cara lain, namun saya lebih memilih memasang aplikasi Dapodik dalam VirtualBox dengan Windows 7 sebagai _guest_ dan melakukan [_port forwarding_](https://en.wikipedia.org/wiki/Port_forwarding) ke _host_. Dengan demikian saya bisa mengerjakan Dapodik langsung melalui _guest_ dan atau melalui peramban di _host_.

Untuk melakukan _port forwarding_ dari _guest_ (Windows 7) VirtualBox ke _host_ (Linux), ikuti langkah berikut;  
Pertama, kita harus tahu IP dari _guest_. Karenanya jalankan _guest_ dan jalankan perintah `ipconfig` dalam _command line_ di _guest_.

[![ipconfig]({{ site.baseurl }}/assets/img/cmd-ipconfig.png)]({{ site.baseurl }}/assets/img/cmd-ipconfig.png)

Dari gambar di atas kita dapat IP adalah `10.0.2.15`.

Dapodikdas versi 2016 dijalankan dalam _localhost_ pada _port_ `5774` di _guest_. Untuk _host_ baiknya kita gunakan _port_ yang tidak digunakan, misalnya `8080`. 

Dicontohkan menggunakan `VBoxManage` untuk _port forwarding_ Dapodik di _virtual machine_ bernama Windows 7 dengan IP `10.0.2.15` dan _port_ `5774` ke _localhost_ dengan IP `127.0.0.1` dan _port_ `8080`.

```sh
VBoxManage modifyvm "Windows 7" --natpf1 Dapodikdas,tcp,127.0.0.1,8080,10.0.2.15,5774  
```

_That’s it!_  
Dalam hal ini _guest_ berfungsi sebagai _server_ dan _host_ sebagai _client_. Untuk mengerjakan Dapodik di _host_ mesti menjalankan _guest_ terlebih dahulu, baru kemudian membuka _browser_ di _host_ dan masukkan alamat `http://localhost:8080`.  

[![dapodik]({{ site.baseurl }}/assets/img/vbox-port-forward-dapodik.png)]({{ site.baseurl }}/assets/img/vbox-port-forward-dapodik.png)

Meski bisa mengerjakan Dapodik secara simultan dalam _guest_ dan _host_, namun untuk sinkronisasi tetap harus melalui _guest_ (Windows 7).
