---
layout: post
title: "Linux Terminal Komutları I"
date: 2013-04-07 23:07
comments: true
categories: 
---
Az çok linux(Unix) işletim sistemi ile ilgisi olanlar komut satırını kullanmanın sağladığı gücü bilir.
Bizde terminal komutlarını giriş yapalım. Burda temel komutları anlatmayı deneyeceğim. Bende daha öğrenme aşamasında olduğum için
hatamız olursa kusuruma bakmayın.
<!-- more -->

### cd

Açılımı *Change Directory* Türkçe meali ile "Dizin Değiştir".


{% highlight bash %}
    
    cd /Dosya/yolu #=> Yolu verilen dizine götürür.
    cd             #=> Home(ev) dizinine götürür.
    cd ..          #=> Bir üst dizine götürür.

{% endhighlight %}


### pwd

Açılımı *Print Working Directory* Türkçe meali ile "Çalışma Dizinini Yazdır"

{% highlight bash %}
    
    pwd            #=> İçinde bulunulan dizini gösterir.
    /home/mhmt/Downloads

{% endhighlight %}

### ls

Açılımı *Change Directory* Türkçe meali ile "Dizin Değiştir".


{% highlight bash %}
    
    ls            #=> İçinde bulunulan dizindeki dosya ve klasörleri listeler.
    ls -a         #=> Bütün gizli dosyalar ve klasörlerle beraber  listeler.
    ls -l         #=> Dosyaların izinlerini, sahiplerini, boyutlarını
                  #=> ve son güncellenme tarihlerini ile beraber listeler.
    ls -d         #=> Sadece dizinleri listeler.
    ls -ld        #=> Verilen dizinin bilgilerini listeler.
    ls /dizin     #=> Verilen dizinin içini listeler.

{% endhighlight %}


### mkdir

Açılımı *Make Directory* Türkçe meali ile "Dizin Oluştur".

{% highlight bash %}
    
    mkdir dizin           #=> Dizin adlı klasör oluşturur.
    mkdir -p foo/bar/baz  #=> İç içe dizinler oluştur.
    
{% endhighlight %}

### touch

*touch* verilen isimde dosya oluştur. Eğer dosya varsa günceller.

{% highlight bash %}
    
    touch foo.txt          
       
{% endhighlight %}

### rm 
"ReMove" verilen dizin yada dosyayı siler.

{% highlight bash %}
  
    rm foo.txt          #=> foo.txt'i siler.
    rm -d               #=> Boş dizini siler.
    rm -f               #=> Dizinin boş yada dolu olmasını umursamaz.
    rm -r               #=> İç içe dizinleri siler.

       
{% endhighlight %}



