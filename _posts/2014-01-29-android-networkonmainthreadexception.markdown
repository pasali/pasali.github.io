---
layout: post
title: "Android - NetworkOnMainThreadException"
date: 2014-01-29 12:55
comments: true
keywords: android, network, thread, exception
---
Eğer network işlemleri yapan bir android uygulaması geliştiriyorsanız, büyük ihtimal ile yukarıda başlıkta yazdığım hata ile 
karşılaşmıssınızdır. <!-- more -->
Aslında internette çok küçük bir araştırma yaparak bu konu hakkında yazılmış sorulara ve verilen cevaplara ulaşabilirsiniz.

Ben biraz bu hata nedir ondan bahsedeyim. Kabaca ismine bakarsak, “ana threadte(iş parçasında) network işlemi yapıyorsun” diyor. Android işletim sistemini geliştiren abilerimiz buna izin vermemişler. Bu şekilde tasarlamalarının sebebi ise ağır işlerin ana threadte yapılmasının performansı düşürmesidir.

Peki bu hata nasıl çözülür? Temelde 3 yolu bulunmakta:

- StrictMode kullanarak Thread politikasını değiştirmek
- Başka bir Thread oluşturmak
- ASyncTask kullanmak


Birinci yol pek tavsiye edilen bir çözüm değil. Nedenine gelirsek bunu yaparak basitçe şunu diyorsunuz “Ben, Android’i geliştiren abilerden daha iyi biliyorum. Ana threadte ağır işlemleri yaparsam performans kaybı olmaz.” Bu probleme kötü bir yaklaşım, uygulamanız inanılmaz derecede yavaş olursa benden günah gitti. :)

```java
StrictMode.ThreadPolicy policy = new
StrictMode.ThreadPolicy.Builder().permitAll().build();

StrictMode.setThreadPolicy(policy);
```
İkinci yol ise sıradan Thread kullanımı;

```java
Thread networkIslemi = new Thread(new Runnable(){
    @Override
    public void run() {
        try {
           /* Kodlarınız burada olacak */
        } catch (Exception e) {
           e.printStackTrace();
        }
    }
});
networkIslemi.start();
```
Üçüncü yol AsyncTask ise aslında android için özelleştrilmiş thread kullanımı diyebiliriz.
Normal threadlerden farklı olarak AsyncTask kullanarak işlemi aşama aşama takip edebilirsiniz.
İlereyen günlerde bu konu ile ilgili bir örnek yazmayı düşünüyorum. Şimdilik 
[burdan](http://developer.android.com/reference/android/os/AsyncTask.html)
