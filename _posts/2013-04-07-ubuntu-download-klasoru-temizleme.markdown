---
layout: post
title: "Ubuntu Download Klasörü Temizleme"
date: 2013-04-07 23:08
comments: true
categories:
---
Boş zamanlarında sürekli benim gibi film-dizi izleyenlerin genelde (en azından bende öyle) indirilenler klasöründe
altyazılar,torrent dosyaları, arşivler ve film afişleri gibi bir sürü tek kulanımlık dosyalar olur. Her seferinde elle silmek biraz zaman aldığından bu işi benim için yapacak ufak bir kabuk betiği yazdım.
<!-- more -->
Kodlarımız şöyle:
``` bash
#!/bin/bash
case "$1" in
   "-s")
        echo " 'srt' ve 'sub' uzantılı dosyalar silenecek?(e/h)"
        read secim
        if [ "$secim"==[eE] ]
        then
            rm -rf $HOME/Downloads/*.srt && rm -rf $HOME/Downloads/*.sub
        fi
    exit 1
    ;;
    "-t")
        echo "'torrent' uzantılı dosyalar silenecek?(e/h)"
        read secim
        if [ "$secim"==[eE] ]
        then
            rm -rf $HOME/Downloads/*.torrent
        fi
    exit 1
    ;;
    "-a")
        echo "Downloads dizinindeki tüm  dosyalar silenecek?(e/h)"
        read secim
        if [ "$secim"==[eE] ]
        then
            rm -rf $HOME/Downloads/*
        fi
        ;;
    "-n")
        path=$(find Downloads/ -type f -name $2)
        echo $path
        echo "Silinsin mi?(e/h)"
        read secim
        if [ "$secim"==[eE] ]
        then
            rm -rf "$path"
        fi
        ;;
    "-e")
        echo "$2 uzantılı dosyalar silinecek?(e/h)"
        read secim
        if [ "$secim"==[eE] ]
        then
            rm -rf Downloads/*."$2"
        fi
        ;;
esac
```

<h3>Kodu Çalıştırma</h3>


  Öncelikle ev dizinde bin adında bir klasör oluşturun.<br>
  Betiği bin klasörünün içine atın.<br>
  Sonra betiğe çalışma izini verin.<br>
```bash
    sudo chmod +a bin/[dosya_ismi]
```
.bashrc dosyasına alttaki satırı ekleyin
```bash
    PATH=$PATH:$HOME/bin
```
artık dosya ismini yazarak betiği çalıştırabilirsiniz.<br>

<h3>Kullanımı</h3>


**-t**   => torrent uzantılı dosyaları siler.<br>
**-s**   => srt ver sub uzantlı dosyaları siler.<br>
**-a**   => bütün dosyaları siler.<br>
**-n**   => verilen isimli dosyayı siler.<br>
**-e**   => verilen uzantılı dosyaları siler.<br>


Not:

Ubuntuyu Türkçe kullanıyorsanız. Downloads'ı kendinizdeki isimle değiştirin.

