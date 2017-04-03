---
layout: post
title: Nyinglar Hahalang Internet Sehat Indosat di Linux
tags: [sunda]
comments: true
---

Hapunten anu kasuhun, réhna sim kuring ayeuna nulis artikelna maké basa Sunda. Itung-itung ngamumulé basa daérah nu ayeuna geus pada ninggalkeun.  
Naon atuh anu bakal dipedar dina tulisan poé ayeuna?

Sok, coba tingali, gambar naon ieu di handap? Pernah manggihan gambar siga kitu pas keur internétan?

[![Internet Sehat]({{ site.baseurl }}/assets/img/internet_sehat.jpg)]({{ site.baseurl }}/img/internet_sehat.jpg)

Cenah mah éta téh ngaranna _"Internét Séhat"_. Duka naon tah maksudna, sim kuring moal jero-jero mesék. Anu puguh mah jadi asa teu pararuguh, geus mah internét mahal, léod ngeteyep siga kuya batok, bari jeung dihahalang, teu meunang kaditu, teu meunang kadieu.

Prak ayeuna urang nyiar kumaha carana supaya bisa najong éta internet séhat supaya ulah ngan ngahalangan jajalaneun baé. Sim kuring ngan nyoba ieu tarékah di handap dina GNU/Linux Debian Sid jeung operator sélulér Indosat, duka tah lamun diterapkeun kana jaringan operator séjén mah.

Sok, mangga urang tengetan prak-prakanana...

- Lamun keur internétan, wayahna pareuman heula. Lamun henteu, léngkah ieu dihandap bakal siga ngawurkeun watu kana keusik, mubazir. 
- Buka berkas `/etc/resolv.conf`. Urang contokeun mukana make `nano`.

  ```bash
  sudo nano /etc/resolv.conf
  ``` 
  
- Ganti eusina maké DNS salian ti DNS operator sélulér. Naon baé tah? Sakanyaho sim kuring, aya tilu anu bisa dipaké salaku gaganti téh, nyaéta:
  - [OpenDNS](https://www.opendns.com/)
  
    ```bash
    nameserver 208.67.222.222
    nameserver 208.67.220.220
    ```
  - [dns.watch](https://dns.watch)
  
    ```bash
    nameserver 84.200.69.80
    nameserver 84.200.70.40
    ```
  - [OpenNIC](https://www.opennicproject.org)
  
    ```bash
    nameserver 52.230.17.182
    nameserver 45.63.25.55
    ```
- Lamun geus dirobah maké salah sahiji DNS di luhur, simpen maké cara teken tombol kibor `CTR+O` babarengan. Kaluar tina `nano` ku cara neken `CTRL+X`.

- Ganti atribut `/etc/resolv.conf` supaya teu beunang dirobah eusina.

  ```bash
  sudo chattr +i /etc/resolv.conf 
  ```

- Tah, ayeuna sambungan internétna bisa dihirupkeun deui. Cing jajal muka situs-situs anu tadina dihalangan ku internét séhat, _insya Allah_ bisa.

Cag, urang tepikeun nepi kadieu heula. Mugia isuk jaganing pagéto bisa deui kaparengkeun nulis make basa Sunda. Wilujeng internétan anu séhat saéstuna, permios...
  
  
    
    
    
    
    
    
    
    
    
    
    
