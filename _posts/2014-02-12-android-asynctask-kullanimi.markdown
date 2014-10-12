---
layout: post
title: Android - AsyncTask Kullanımı
date: 2014-02-12 15:17
comments: true
categories: 
---

`AsyncTask`, ana thread ile arka plan işlemlerini birleştirmemize sağlıyan güzide
bir Android sınıfı. Bilindiği üzere bazı sebeblerden (mesela `performans`) dolayı
Android'i geliştiren abilerimiz ana thread bazı işlemleri yapmamızı
yasaklamışlar(`Network` işlemleri gibi). Kullanmamız için bize `Thread` sınıfını
özelleştirip `AsyncTask` sınıfını sunmuşlar. `Thread`lerden farklı olarak
arka plan işlemi hakkında aşama aşama bilgi sahibi olabiliyoruz. <!-- more -->  

### Nasıl Kullanılır ?

        private class ArkaplanIslemi extends AsyncTask<var1, var2, var3>

Önce yukardaki gibi sınıfızı AsyncTask sınıfından extends ediyoruz. Burada
bilmeniz gereken var1, var2 ve var3 un ne olduğu.  

- var1 = Ana threadten AsyncTask verilecek değişken( örneğin dosya indirme
  işlemi yapılacaksa URL)
- var2 = Arka plan işlemi sürerken aşamalar arasında bilgi alışverişi için
  kullanılacak değişken( 100 dosya inecek sizde her 25 dosya indiğinde kullanıcı
  bildirmek istiyorsanız int verebilirsiniz.)
- var3 = Arka plan işlemi bittiğinde ana threade dönülecek değişken tipi

Bunları söyledikten sonra artık AsyncTask'ın aşamlarına geçebiliriz.

#### onPreExecute()

Bu aşamada daha arkaplan işlemi başlamamıştır. Ana thread üzerinde değişiklik
yapabilirsiniz. Örneğin; bir TextView'in içeriğini "indirme işlemi başlıyor"
olarak değiştirebilirisiniz.

#### doInBackground(var1...)

Bu aşamada artık arkaplan işlemi başlamış durumda. Ana thread'ten alınan veriler
burda işleniyor.

#### onProgressUpdate(var2...)

Bu aşamada ise arkaplan işlemi devam ederken, ana thread'e (yani kullanıcıya)
bilgi verme işlemi yapılır. Örneğin; "50 dosya indi." gibi.

#### onPostExecute(var3...)

Artık arka plan işlemi bitmiştir. Sonucun ana thread'e döndürüldüğü aşamadır.

Sınıfı çağırma işlemide:

        ArkaplanIslemi.execute(argumanlar...)

