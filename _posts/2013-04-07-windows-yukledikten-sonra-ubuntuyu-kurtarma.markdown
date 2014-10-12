---
layout: post
title: "Windows Yükledikten Sonra Ubuntuyu Kurtarma"
date: 2013-04-07 23:02
comments: true
categories: 
---

*Ubuntu*'nun üzerine *Windows* kurdunuz ve artık bilgisayar açılırken size işletim sistemi seçiminizi
sormadan *Windows* mu açılıyor ?
 Yakın zamanda bu sorunu yaşamış biri olarak size dilim döndüğünce yardımcı olmaya çalışayım.

 Öncelikle elinizde bir **Ubuntu liveCD** olmadığını var sayıyorum ve *Windows* üzeriden neler yapabileceğimiz kısımdan yani *Ubuntu*yu nasıl *Windows*un boot menünüsüne ekleriz ondan
bahsedeyim.
<!-- more -->

 EasyBCD adılı program sayesinde bu işlemi gerçekleştirebiliriz.
 [Burdan](http://download.chip.eu/tr/EasyBCD-1.72_177662.html) free sürümünü indirebilirsiniz.

 İndirme işlemini geçtikten sonra şimdi *Ubuntu*nuzua erişmeninizi sağlayacak adımlara geçebiliriz.

 Programı kurduktan sonra karşınıza şöyle bir ekran gelecek.

 ![EasyBCD1](/images/resim1.jpg)

 Sonra soldaki menüden **Add New Entry( Yeni Giriş Ekle)** butonuna tıklıyoruz.

 ![EasyBCD2](/images/resim2.jpg)

 Sonra açılan pencerede **Operating Systems (İşletim Sistemleri)** menüsünden **Linux/BSD** tabına tıklıyoruz.

 **Type** sekmesine **Grub 2** seçip, **Name** kısmına da boot menüsünde görünecek ismi yazıyoruz.(*Ubuntu 10.04 gibi*)

 Son olarak da **Add Entry**ye tıklayıp ekleme işlemini tamamlıyoruz.

 ![EasyBCD3](/images/resim3.jpg)

 **Edit Boot Menu** kısmından da öntanımlı işletim sistemini ayarlayabiliriz.

 ![EasyBCD3boot](/images/resim4.jpg)

 Burdaki işimizi bittikten sonra bilgisayarınızı yeniden başlattığınızda karşınıza şöyle bir boot ekranı gelmesi lazım.

 ![Easybootek](/images/bootekrani.png)


 *LiveCD*niz varsa seçenekleriniz artıyor. Bunlardan biride **Boot-Repair** yazılımını kullanmak.

 **Boot-Repair**i yüklemek için aşığdaki adımları takip edebilirsiniz.

 *LiveCD*nizi takıp bilgisayarınızı CDden boot edin.

 *Try Ubuntu* yada Türkçesiyle *Ubuntuyu dene* butonuna tıklayın.

  ![Bootekrani](/images/livecd.jpg)

 **Ctrl+alt+t** ile terminali açıp alttaki kodu girin.

    sudo add-apt-repository ppa:yannubuntu/boot-repair && sudo apt-get update
    sudo apt-get install -y boot-repair

  Eğer hiçbir hata almadıysanız **Boot-Repair** sisteminize kurulmuştur.
  Terminalde

    boot-repair

  komutunu verin karşınıza aşığıdaki gibi bir pencere gelcek.
 ![Boot-Repair](/images/boot-repair.png)

 **Recommended Repair** butonuna tıkalyıp gerisini programa bırakıyoruz.
 İnternetiniz yoksa bu yolu kullanamayacaksınız.Grubu elle düzeltmek zorundayız.Yine terminalimizi açıp:

    sudo grub-install /dev/XXX

 Burdaki **XXX** *Ubuntu*nun sizin bilgisayarınızda bulunduğu  bölümün ismini temsil ediyor.(*sda3 gibi*)
  Bunu bölümün ismini öğrenmek için terminaldeyken:

    sudo fdisk -l
  Buna benzer bir çıktı almanız lazım.

  ![fdisk](/images/fdisk.png)

  Benim bilgisayarımda **sda3** bölümünde *Ubuntu* kurulu.Yani:

    sudo grub-install /dev/sda3

  şeklinde olmalı.

 daha fazlası [burada](https://help.ubuntu.com/community/RecoveringUbuntuAfterInstallingWindows)
