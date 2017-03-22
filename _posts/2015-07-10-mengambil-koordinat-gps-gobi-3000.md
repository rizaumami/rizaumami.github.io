---
layout: post
title: Mengambil Koordinat GPS Gobi 3000
tags: [gps, thinkpad, modem]
comments: true
---

Meski lini ThinkPad masih merupakan pilihan nomor satu bagi pengguna Linux karena kompatibilitasnya, namun Lenovo selaku pemegang merek nampaknya lebih mengutamakan Windows. Bisa dilihat dari komponen ThinkVantage yang merupakan *software-software* pendukung ThinkPad yang hanya ada untuk Windows, semisal `ThinkVantage System Update`, `ThinkVantage GPS`, dan `ThinkVantage Access Connection`.

Namun tidak berarti pengguna Linux tidak dapat menggunakan ThinkPad secara optimal. Ada banyak perangkat lunak dalam GNU/Linux yang dapat menjadi pengganti bagi *software* khusus ThinkPad yang disediakan Lenovo.

Artikel ini dikhususkan membahas bagaimana mendapatkan koordinat GPS dari modul WWAN Gobi 3000. Sayangnya, *AFAIK*, tidak ada aplikasi GPS yang *sleek* di Linux, jadi di sini kita hanya akan mengambil koordinat dan merubahnya ke dalam format laman Googel Maps agar kita mendapat visualisasi tempat dari layanan Google Maps.

Koordinat GPS kita dapatkan dari modul Gobi 3000 dengan bantuan `ModemManager` yang memiliki kemampuan ini sejak versi 0,5. Koordinat yang disajikan `ModemManager` berupa bakuan NMEA yang kurang *human readable*, jadi perlu kita rubah hingga dapat lebih mudah dicerna manusia.

Lihat kemampuan apa saja yang dimiliki Gobi 3000:

{% highlight sh %}
mmcli -m 1 --location-status
{% endhighlight %}

Ganti nomor modem (`1`) dengan angka lain jika mendapati galat `error: couldn't find modem at '/org/freedesktop/ModemManager1/Modem/1'`.
Berikut keluaran yang diharapkan:

{% highlight sh %}
/org/freedesktop/ModemManager1/Modem/1
  ----------------------------
  Location | capabilities: '3gpp-lac-ci, gps-raw, gps-nmea'
           |      enabled: 'none'
           |      signals: 'no'
{% endhighlight %}

Keluaran menandakan Gobi 3000 mendukung `3GPP Location Area Code/Cell ID`, `GPS raw`, dan `GPS NMEA`. `enabled: 'none'` menandakan GPS belum diaktifkan. Aktifkan menggunakan perintah:

{% highlight sh %}
sudo mmcli -m 1 --enable
{% endhighlight %}

Jika berhasil, akan nampak `successfully enabled the modem`.

Aktifkan juga kemampuan modem.
 
{% highlight sh %}
sudo mmcli -m 1 --location-enable-gps-raw --location-enable-gps-nmea
{% endhighlight %}

Jika berhasil, akan tampak `successfully setup location gathering`.
Saya tidak mengaktifkan `--location-enable-3gpp-lac-ci` karena selalu tidak berhasil mendapat koordinat, entah karena lokasi entah karena modul Gobi 3000 tidak *fully support*.

Setelah modem dan kemampuan lokasinya aktif, saatnya untuk mendapat koordinat GPS. Bawa latpop ke luar ruangan, atau setidaknya sedikit objek yang dapat menghalangi sinyal satelit GPS, dan jalankan perintah berikut:

{% highlight sh %}
sudo mmcli -m 1 --location-get
{% endhighlight %}

Setelah beberapa saat mendeteksi satelit GPS, hasilnya akan seperti berikut:

{% highlight sh %}
/org/freedesktop/ModemManager1/Modem/1
  -------------------------
  3GPP location   | Mobile country code: '214'
                  | Mobile network code: '3'
                  |  Location area code: '21071'
                  |             Cell ID: '7033737'
  -------------------------
  GPS NMEA traces | $GPGGA,,,,,,0,,,,,,,,*66
                  | $GPRMC,,V,,,,,,,,,,N*53
                  | $GPGSA,A,1,,,,,,,,,,,,,,,*1E
                  | $GPGSV,4,1,16,24,,,,29,,,,05,,,,18,,,*7A
                  | $GPGSV,4,2,16,22,,,,14,,,,11,,,,17,,,*7B
                  | $GPGSV,4,3,16,03,,,,12,,,,30,,,,13,,,*78
                  | $GPGSV,4,4,16,23,,,,15,,,,27,,,,07,,,*79
                  | $GPVTG,,T,,M,,N,,K,N*2C
  -------------------------
  Raw GPS         |  UTC time: '155142.2'
                  | Longitude: '-3.513941'
                  |  Latitude: '40.502603'
                  |  Altitude: '18.000000'
{% endhighlight %}

Dari keluaran tersebut kita mengetahui; data diambil pukul 155142 UTC atau 23.51 WIB, 18 m dari permukaan laut, garis bujur 40,5° dan garis lintang -3,5°.

Tapi, di manakah itu?
Koordinat saja memang sulit untuk digambarkan orang awam. Karenanya, baik kita buat sebuah *script* untuk merubah sandi NMEA ke dalam format yang dapat diajukan ke Google Maps agar ia dapat menggambarkan di mana letak tersebut berada.

*Here's the script*, saya tidak akan membahas cara kerja *script* tersebut, jika Anda penasaran silakan rujuk laman berikut:

* [http://www.thinkwiki.org/wiki/Qualcomm_Gobi_2000](http://www.thinkwiki.org/wiki/Qualcomm_Gobi_2000)
* [http://wiki.openmoko.org/wiki/Getting_GPS_console_output_with_gllin](http://wiki.openmoko.org/wiki/Getting_GPS_console_output_with_gllin)
* [http://gpsinformation.org/dale/nmea.htm](http://gpsinformation.org/dale/nmea.htm)
* [https://sigquit.wordpress.com/2012/03/29/enabling-gps-location-in-modemmanager/](https://sigquit.wordpress.com/2012/03/29/enabling-gps-location-in-modemmanager/)

{% highlight sh %}
#!/bin/bash

# script untuk mendapatkan koordinat GPS dari WWAN Gobi 3000 Sierra MC8355
#
# http://www.thinkwiki.org/wiki/Qualcomm_Gobi_2000
# http://wiki.openmoko.org/wiki/Getting_GPS_console_output_with_gllin
# http://gpsinformation.org/dale/nmea.htm
# https://sigquit.wordpress.com/2012/03/29/enabling-gps-location-in-modemmanager
#
# Requires GNU awk and modemmanager
#
# Copyright (c) 2015 Sahri Riza Umami riza@openmailbox.org
#
# License:  This script is distributed under the terms of version 3 of the GNU GPL.

# VARIABLES --------------------------------------------------------------------

CMD=$(basename $0)
MODEM=$(awk '/MC8355/{print $1}' <(mmcli -L))

# FUNCTIONS --------------------------------------------------------------------

print_help() {
cat <<EOF

  getpos    Script untuk membaca GPS WWAN Sierra Wireless MC8355

  Usage:    $CMD [options]

    gga     Essential fix data which provide 3D location and accuracy data.
    gsa     GPS DOP and active satellites.
    gsv     Satellites in View shows data about the satellites that the unit.
            might be able to find based on its viewing mask and almanac data.
    rmc     NMEA version of essential gps pvt (position, velocity, time) data.
    vtg     Velocity made good  

EOF
}

get_gga() {
awk -F, -v OFS='\n' '/\$GPGGA/ {print \
"Global Positioning System Fix Data",\
"",\
strftime("Date\t\t\t\t\t: %d %B %Y"),\
"Fix taken at (GMT+7)\t\t\t: "(substr($2,0,2) + 7)":"(substr($2,3,2))":"(substr($2,5,2)),\
"Latitude\t\t\t\t: "(substr($3,0,2))"\xc2\xb0 "(substr($3,3))"\x27 "$4,\
"Longitude\t\t\t\t: "(substr($5,0,3))"\xc2\xb0 "(substr($5,4))"\x27 "$6,\
"Fix quality\t\t\t\t: "$7,\
"Number of satellites being tracked\t: "$8,\
"Horizontal dilution of position\t\t: "$9,\
"Altitude, above mean sea level\t\t: "$10"m",\
"Height of geoid above WGS84 ellipsoid\t: "$12,\
"Google Maps URL\t\t\t\t: ","\thttps://maps.google.com/maps?t=k&q=loc:-"(substr($3,0,2)+(substr($3,3)/60.0))"+"(substr($5,0,3)+(substr($5,4)/60.0)); fflush();}' <(mmcli -m "$MODEM" --location-get-gps-nmea)  
}

get_gsa() {
awk -F, -v OFS='\n' '/\$GPGSA/ {print \
"Satellite status",\
"",\
"Auto selection of 2D or 3D fix\t: "$2,\
"3D fix\t\t\t\t: "$3,\
"PRNs of satellites used for fix\t: "$4","$5,\
"PDOP (dilution of precision)\t: "$16,\
"Horiz. dilution of precision\t: "$17,\
"Vertical dilution of precision\t: "(substr($18,0,3)); fflush();}' <(mmcli -m "$MODEM" --location-get-gps-nmea)
}

get_gsv() {
awk -F, -v OFS='\n' '/\$GPGSV/ {print \
"Satellites in view",\
"",\
"Num. of sentences for full data\t: "$2,\
"Sentence 1 of 2\t\t\t: "$3,\
"Number of satellites in view\t: "$4,\
"Satellite PRN number\t\t: "$5,\
"Elevation\t\t\t: "$6"\xc2\xb0",\
"Azimuth\t\t\t\t: "$7"\xc2\xb0",\
"Signal to Noise Ratio\t\t: "$8,""; fflush();}' <(mmcli -m "$MODEM" --location-get-gps-nmea)
}

get_rmc() {
awk -F, -v OFS='\n' '/\$GPRMC/ {print \
"Recommended Minimum sentence C",\
"",\
"Fix taken at (GMT+7)\t\t: "(substr($2,0,2) + 7)":"(substr($2,3,2))":"(substr($2,5,2)),\
"Status\t\t\t\t: "$3,\
"Latitude\t\t\t: "(substr($4,0,2))"\xc2\xb0 "(substr($4,3))"\x27 "$5,\
"Longitude\t\t\t: "(substr($6,0,3))"\xc2\xb0 "(substr($6,4))"\x27 "$7,\
"Speed over the ground\t\t: "$8" knots",\
"Track angle\t\t\t: "$9"\xc2\xb0",\
"Date\t\t\t\t: "(substr($10,0,2))"-"(substr($10,3,2))"-"(substr($10,5,2)),\
"Magnetic Variation\t\t: "$11,\
"Google Maps URL\t\t\t: ","\thttps://maps.google.com/maps?t=k&q=loc:-"(substr($4,0,2)+(substr($4,3)/60.0))"+"(substr($6,0,3)+(substr($6,4)/60.0)); fflush();}' <(mmcli -m "$MODEM" --location-get-gps-nmea)  
}

get_vtg() {
awk -F, -v OFS='\n' '/\$GPVTG/ {print \
"Track made good and ground speed",\
"",\
"True track made good\t\t: "$2"\xc2\xb0",\
"Magnetic track made good\t: "$4,\
"Ground speed\t\t\t: "$6" knots",\
"Ground speed\t\t\t: "$8" Kilometers per hour"; fflush();}' <(mmcli -m "$MODEM" --location-get-gps-nmea)
}

# MAIN -------------------------------------------------------------------------

mmcli -m "$MODEM" --location-enable-gps-nmea 1>/dev/null

case "$1" in
  gga)
    get_gga
  ;;
  gsa)
    get_gsa
  ;;
  gsv)
    get_gsv
  ;;
  rmc)
    get_rmc
  ;;
  vtg)
    get_vtg
  ;;
  *)
    print_help
  ;;
esac

{% endhighlight %}
