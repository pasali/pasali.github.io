---
layout: post
title: "Android - Bildirim(Notification) Oluşturma"
date: 2014-01-31 17:11
comments: true
categories: [android, notification, bildirim, oluşturma]
---
Android uygulama geliştirirken biraz daha kullanıcı dostu olmak için
bildirimleri kullanabilirsiniz. Kullanıcı dostu olmanın yanında uygulamanın
türüne göre kullanıcıya istediğiniz durumlarda bilgi verebilirsiniz. Mesela bir
doviz uygulaması geliştiriyorsunuz ve dolar belirli bir seviyenin üstüne
çıktığında ya da düştüğünde kullanıcıyı bilgilendirmek isteyebilirsiniz.   

Oluşturma işlemini adımlara bölersek: <!-- more -->

- Bildirimi tıklanınca açılcak sınıfı ayarlama
- Başka bir buton eklenecekse onun için açılcak sınıf ve ya çalışacak
  fonskiyonu ayarlama
- Bildirimi oluşturma
- Uyarı türünü belirleme ( sesli, titreşim vb)
- Son aşamada bildirimi yayınlama

```java
/* Bildirime tıklanınca açılacak activity */
Intent Oku_intent = new Intent(this, AcilacakActivity.class);
PendingIntent p_oku = PendingIntent.getActivity(this, 0, Oku_intent, 0);
/* Sil butonu için Broadcast mesajı  */
Intent Sil_intent = new Intent();
Sil_intent.setAction("com.deneme.uyg.DEL_INTENT");
PendingIntent p_sil = PendingIntent.getBroadcast(this, 0, Sil_intent, 0);
/* Bildirimi oluşturmaya başlıyoruz */
Notification bildirim = new Notification.Builder(this)
                /* Bildirim başlığı */
                .setContentTitle("Mesaj geldi!!")
                /* Bildirim için icon ve intent atama ( drawable klasoru içinde resim1.jpg olması lazım) */
                .setSmallIcon(R.drawable.resim1).setContentIntent(p_oku)
                /* Sil butonu için icon ve intent ataması */
                .addAction(R.drawable.del, "Sil", p_sil).build();
/* Bildirim yayınlamak için bir NotificationManager sınıfını kullanacağız */
NotificationManager notificationManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
/* Bildirim otomatik yok olacak tıklanmadan sonra */
bildirim.flags |= Notification.FLAG_AUTO_CANCEL;
/* Sesle uyarı yapacak */
bildirim.defaults |= Notification.DEFAULT_SOUND;
/* artık yayınlama işlemini gerçekleştirebiliriz */
notificationManager.notify(0, bildirim);
```
Burada önemli olan bildirimi yayınlarken veridiğimiz `0` değeri. Bildirimin id değerini belirtiyor. Bu bildirimi silme ya da güncelleme işlemi yaparken ona ihtiyaç duyacağız.
Sonunda aşığıdaki gibi bir çıktı elde etmemiz gerekiyor.   
![resim](/images/noti.png)

