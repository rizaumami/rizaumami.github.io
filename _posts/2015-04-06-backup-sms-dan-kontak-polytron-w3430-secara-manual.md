---
layout: post
title: Backup/Restore SMS dan Kontak di Polytron W3430 Secara Manual
tags: [polytron, android, backup]
comments: true
---

Pastikan hape sudah di-*root*.
Penerapan `chown` dan `chmod` dengan menggunakan *terminal emulator* atau *file manager* yang mendukung.

## Backup Kontak

{% highlight sh %}
cp -a /data/data/com.android.providers.contacts /sdcard/backups
{% endhighlight %}

## Backup SMS

{% highlight sh %}
cp -a /data/data/com.android.providers.telephony /sdcard/backups
{% endhighlight %}

## Restore Kontak

{% highlight sh %}
cp -fa /sdcard/backups/com.android.providers.contacts /data/data
chmod -R 660 /data/data/com.android.providers.contacts
chown -R app_0.app_0 /data/data/com.android.providers.contacts
{% endhighlight %}

## Restore SMS

{% highlight sh %}
cp -fa /sdcard/com.android.providers.telephony /data/data
chmod -R 660 /data/data/com.android.providers.telephony
chown -R radio.radio /data/data/com.android.providers.telephony
{% endhighlight %}

