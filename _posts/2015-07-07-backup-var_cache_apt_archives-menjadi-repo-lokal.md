---
layout: post
title: Backup /var/cache/apt/archives Menjadi Repo Lokal
tags: [debian, backup, script]
comments: true
---

*Well, internet is expensive*. Sayang rasanya jika paket yang telah diunduh
dihapus begitu saja.

*Script* ini akan mencadangkan paket unduhan dalam `/var/cache/apt/archives`
ke tempat yang ditentukan (sesuai pengaturan `local_mir`) dengan hierarki sesuai
*mirror* dan dapat digunakan sebagai *source* bagi `debootstrap`.

Sebenarnya ada `apt-move` untuk tugas ini, namun ia memiliki *bug* yang membuatnya
gagal bekerja dan tiada yang sudi memperbaiki.

Pastikan `apt-utils` terpasang untuk menyediakan `apt-ftparchive`.

{% highlight sh %}

#!/usr/bin/env bash

## variables -------------------------------------------------------------------

## sesuaikan local_mir dengan path local mirror
local_mir=~/Software/mirrors/debian
src_list=/etc/apt/sources.list
deb_list=/var/lib/apt/lists
deb_dir=/var/cache/apt/archives
temp_dir=/tmp/repo_$(date +%F_%H-%M-%S)

## functions -------------------------------------------------------------------

print_help() {
cat >&2 <<- EOF

  Usage:   repo COMMAND

  Commands:
    edit        - sunting /etc/apt/sources.list
    local       - gunakan repository lokal
    online      - gunakan repository online
    copy        - salin paket dari cache ke repo lokal
    move        - pindahkan paket dari cache ke repo lokal
    remove      - buang paket yang tidak ada dalam mirror

EOF
exit
}

cp_deb_to_repo() {

for lists in $(find /var/lib/apt/lists -type f -iname "*amd64_Packages" -printf '%P\n')
do
  lists_deb="${lists//_/\/}"
  mkdir -p "$local_mir/${lists_deb/\/Packages/}"
  cp -vT /var/lib/apt/lists/$lists "$local_mir/$lists_deb"
  find $deb_dir -type f -name "*.deb" -printf '%P\n' > $temp_dir/in_cache
  awk '/Filename/{print $2}' /var/lib/apt/lists/$lists > $temp_dir/in_mirror
  cd "$local_mir/${lists_deb%%dists*}"
  while read paket
  do
    mkdir -p ${paket%/*}
    cp -v $deb_dir/${paket##*/} ${paket%/*}
  done < <(grep -Fwf $temp_dir/in_cache $temp_dir/in_mirror)
done

}

buat_release() {

for pkg_lst in $(find $local_mir -type f -iname "Packages")
do
  cat $pkg_lst | gzip -9c > $pkg_lst.gz
  echo "Architectures: amd64
Codename: sid
Components: main contrib non-free
Description: Repo lokal
Label: Debian
Origin: Debian
Suite: $suite" > ${pkg_lst%%/binary*}/../Release
  apt-ftparchive release ${pkg_lst%%/binary*}/.. >> ${pkg_lst%%/binary*}/../Release
done

}

use_local() {
sudo sed -i '/^deb http/s/^deb http/#deb http/g' $src_list
}

use_online() {
sudo sed -i '/^#deb http/s/^#deb http/deb http/g' $src_list
}

remove_old() {

for deb_lst in $(find $local_mir -type d -iname "pool")
do
  find $deb_lst/../dists/ -type f -name Packages | xargs -I '{}' awk '/Filename/{print $2}' {} > "$temp_dir/in_mirror"
  find $deb_lst/ -type f -name "*.deb" > "$temp_dir/exist"
  grep -v -F -f "$temp_dir/in_mirror" "$temp_dir/exist" | xargs -I '{}' mv -v {} /tmp
done

}

## main ------------------------------------------------------------------------

mkdir -p $temp_dir

case "$1" in
  edit)
    sudo nano /etc/apt/sources.list
  ;;
  clean)
    cp_deb_to_repo
    buat_release
    remove_old
    buat_release
    sudo aptitude clean
  ;;
  copy)
    cp_deb_to_repo
    buat_release
  ;;
  local)
    use_local
  ;;
  move)
    cp_deb_to_repo
    buat_release
    sudo aptitude clean
  ;;
  online)
    use_online
  ;;
  remove)
    remove_old
    buat_release
  ;;
  *)
    print_help
  ;;
esac

rm -r $temp_dir

{% endhighlight %}
