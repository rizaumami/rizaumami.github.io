---
layout: post
title: Mengunci GRUB Boot Loader
tags: [grub]
comments: true
---

Sebenarnya, tidak ada cara yang lebih ampuh mengamankan data di komputer selain dengan mengenkripsi HDD/SSD. Namun, nyatanya hingga kini saya enggan untuk melakukan enkripsi pada sarana penyimpanan data tersebut, salah satunya karena takut lupa _passphrase_ atau apalah hingga berakibat HDD/SSD tidak berguna lagi.

Jadi, untuk sementara, saya hanya mengunci BIOS dan GRUB. Mengapa mengunci GRUB? Karena seseorang bisa _boot_ sebagai `root` hanya dengan menyunting _boot entry_ dan menambahkan parameter `single` di ujungnya.

Jika Anda juga menggunakan GRUB2 dan ingin menguncinya dari tangan yang tidak berhak, ikuti langkah mengunci GRUB2 di sistem Debian (_based_) berikut:

- Jalankan `grub-mkpasswd-pbkdf2` untuk mengenkripsi kata kunci bagi GRUB _users_. Berikan kata kunci dan ulangi jika diminta. Setelahnya, kita akan disuguhi _string_ acak yang merupakan enkripsi dari kata kunci yang tadi kita ketikkan.  
Misal hasil dari enkripsi dari _password_ "ngajajaldoang" adalah seperti berikut:

  ```sh
  PBKDF2 hash of your password is grub.pbkdf2.sha512.10000.64B7D4F597498B54FC0F874E01E6B91077001FD0CC351C381CA4545176706846143FABFD01375690633A97A750ADC23ACD41528F26060E3C7542E70FB6892447.065259BA24C87FFA17203F19A29351A90ACCEA9C2B6AAFA4DD2E54D8E3DFA59502ECB61717CD9B899F4E71E8FD496D6048AA4A0F83063E62363170E30D0050E2
  ```

- Simpan _string_ tersebut ke dalam berkas `/etc/grub.d/40_custom`, misalnya:

  ```sh
  set superusers="root"
  password_pbkdf2 root grub.pbkdf2.sha512.10000.64B7D4F597498B54FC0F874E01E6B91077001FD0CC351C381CA4545176706846143FABFD01375690633A97A750ADC23ACD41528F26060E3C7542E70FB6892447.065259BA24C87FFA17203F19A29351A90ACCEA9C2B6AAFA4DD2E54D8E3DFA59502ECB61717CD9B899F4E71E8FD496D6048AA4A0F83063E62363170E30D0050E2
  ```

  Dapat Anda lihat, ternyata ada tambahan baris `set superusers="root"` dan `password_pbkdf2 root`. `root` di sini bukanlah `root` dalam sistem Linux yang akan kita _boot_, melainkan _username_ bagi GRUB _user_ yang akan kita kunci.
  
  Setelah disimpan, mutakhirkan GRUB menggunakan perintah `sudo update-grub`.
  
- Sekarang, tiap kali memilih entri di GRUB kita akan ditanya _user_ dan _password_. Ketikkan `root` sebagai _user_ dan `ngajajaldoang` sebagai _password_ sebagaimana telah diatur di awal.  
Namun jika kita sering _reboot_, proses memasukkan _user_ dan _pass_ ini akan cukup mengganggu.  
Buat pengecualian pada entri tertentu dengan cara menambahkan `--unrestricted` pada baris entri tersebut dalam berkas `/boot/grub/grub.cfg`, atau jika ingin berlaku pada semua entri, tambahkan _string_ `--unrestricted` tersebut pada berkas `/etc/grub.d/10_linux`.  
`/boot/grub/grub.cfg` akan berubah tiap kali perintah `sudo update-grub` dijalankan, dan `/etc/grub.d/10_linux` akan berubah tiap kali paket GRUB mendapat pembaharuan, jadi cara manapun yang diambil, adalah bijaksana setelah melakukan penyuntingan untuk mencadangkan berkas-berkas tersebut. 

  Saya lebih suka untuk menyunting berkas `/etc/grub.d/10_linux` karena ia berlaku menyeluruh. Jadi, berikut dicontohkan untuk menyunting berkas tersebut.  
  String `--unrestricted` harus disisipkan pada baris yang bertanggungjawab mendaftar menu _boot_, gunakan perintah berikut untuk mencari di mana baris tersebut berada:

  ```sh
  grep -in menuentry /etc/grub.d/10_linux
  ```

  Hasilnya akan mirip seperti berikut:
  
  ```sh
  132:      echo "menuentry '$(echo "$title" | grub_quote)' ${CLASS} \$menuentry_id_option 'gnulinux-$version-$type-$boot_device_id' {" | sed "s/^/$submenu_indentation/"
  134:      echo "menuentry '$(echo "$os" | grub_quote)' ${CLASS} \$menuentry_id_option 'gnulinux-simple-$boot_device_id' {" | sed "s/^/$submenu_indentation/"
  ```
  
  Sisipkan `--unrestricted` pada baris `132` dan `134` berkas `/etc/grub.d/10_linux` sesuai pencarian `grep` di atas:
  
  ```sh
  132:      echo "menuentry '$(echo "$title" | grub_quote)' ${CLASS} \$menuentry_id_option 'gnulinux-$version-$type-$boot_device_id' --unrestricted {" | sed "s/^/$submenu_indentation/"
  134:      echo "menuentry '$(echo "$os" | grub_quote)' ${CLASS} \$menuentry_id_option 'gnulinux-simple-$boot_device_id' --unrestricted {" | sed "s/^/$submenu_indentation/"
  ```
  
  Terapkan pengaturan baru ini ke `grub.cfg` menggunakan perintah `sudo update-grub`.  
  _That's it_, kita dapat langsung boot seperti biasa, namun jika hendak melakukan langkah lain semisal menyunting _boot entry_, maka kita akan ditanya _username_ dan _password_.

Demikian artikel kali ini, sebarkan! Jangan sampai manfaatnya terhenti di Anda...
