---
layout: post
title: "Android Sunucuya Terminalden Bağlanma"
date: 2014-01-30 20:28
comments: true
categories: 
---

Android üzerinde çalışan bir sunucuya terminalden nasıl bağlanırız ?   
Öncelikle sunucunun `1234` nolu port üzerinde çalıştığını varsayalım. Uygulamanızı
AVD üzerinde çalıştırın ve sunucuyu aktif hale getirin. <!-- more -->Şimdi `telnet` ile AVD
ye bağlanmamız gerek. AVD'nin port numarası pencere başlığında yazar.(genelde
5554 tür)

        telnet localhost 5554

yukarıdaki komutla AVDye bağlanmış oluyoruz. Eğer işlem başarılı ise bir komut
istemcisi göreceksiniz.

        redir add tcp:5353:1234

Bu komutla ise 1234 portunu(sunucunun çalıştığı port) 5353 numaralı porta
yonlendirmiş oluyor. Şimdi `exit` diyerek terminale dönelim. Artık  aşağıdaki komutla
sunucuyu bağlanalım.

        telnel localhost 5353

Ve işlem tamam :)
