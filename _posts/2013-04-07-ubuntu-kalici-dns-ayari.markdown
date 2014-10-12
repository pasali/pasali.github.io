---
layout: post
title: "Ubuntu Kalıcı DNS Ayarı"
date: 2013-04-07 23:05
comments: true
categories: 
---
**Ubuntu**da DNS değiştirme işlemini konsoldan nasıl yaparız?<br>
Öncelikle **ctrl+alt+t** ile konsolumuzu açıyoruz.
Sonra alttaki kodu giriyoruz.

        sudo gedit /etc/resolv.conf

Burda **gedit** yerine siz hangi metin editörünü kullanıyorsanız onu yazabilirsiniz.<br>
Sonra açılan pencerede isteğiniz DNS **nameserver x.x.x.x** şeklinde yazabilirsiniz.<br>
Örneğin google DNS için:
<!-- more -->

        nameserver 8.8.8.8
        nameserver 8.8.4.4

satırlarını **resolv.conf** dosyasına ekleyin.
Sonrada

        sudo chattr +i /etc/resolv.conf

diyerek herhangi bir şekilde bu dosyanın resetlenmesini engelleyebilirsiniz.(sudo ile bile değiştirilemez olur.)
Bu özelliği kaldırmak içinse

        sudo chattr -i /etc/resolv.conf

kodunu kullanabilirsiniz.

**Not:** 12.04 sürümünde chattr işe yaramıyor.Bunun nedenide resolv.conf dosyasının link olması dosyayı silip yeniden oluşturup yukardaki adımları uyguluyabilrisiniz.<br>
Ya da

        sudo gedit /etc/dhcp/dhclient.conf

Sonarada aşağıdaki yeri değiştirin

        prepend domain-name-servers x.x.x.x, y.y.y.y;

Örnek:

        prepend domain-name-servers 8.8.8.8, 8.8.4.4;

değiştirerek ayarlayabilirsiniz.
