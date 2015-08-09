---
layout: post
title: "Go İle RESTful API Geliştirme"
date: 2015-08-09 19:32
comments: true
---
Başlıktan da anlaşılacağı gibi Go ile basit bir API yazacağız. Yazacağımız API'nin çok fazla fonksiyonu olmayacak sadece başlamak için gerekli bilgileri içerecek. Geri kalan kısmı ise istediğiniz gibi kendiniz yazabileceksiniz. Çalışma ortamınızda Go'nun kurulu olduğunu ve bir Go programını nasıl çalıştırmanız gerektiğini bildiğiniz varsayıyorum. Eğer halihazırda kurulu değilse google küçük bir arama ile gerekli kurulumları yapabilirsiniz.

İlk önce geleneği bozmayıp bir "Hello World" web uygulması yazalım. $GOPATH'nizde `hello-world` adında bir klasor açın. Ve `main.go` isimli  bir dosya oluşturun.

Oluşturduğunuz dosyanın içine aşağıdaki kodları yapıştırın.

```go 

package main

import (
       "fmt"
       "net/http"
)

func rootHandler(rw http.ResponseWriter, req *http.Request) {
       fmt.Fprintf(rw, "Selamlar %s !", req.URL.Path[1:])
}

func main() {
       http.HandleFunc("/", rootHandler)
       http.ListenAndServe(":3333", nil)
}

```
Satır satır açıklamak gerekirse:

- Go'da standalone uygulama yazacaksanız eğer paketinizin ismi `main` olmalıdır.
- `fmt` kütüphanesi i/o işlemleri için, `net/http` ise web ile ilgi fonksiyonları barındıran kütüphanelerdir.
- `rootHandler` gelen request işleyip bir response döndüren controller gibi düşünülebilir.
- `http.HandleFunc("/", rootHandler)` bu satırda ise bir requestMapping işlemi yapılıyor. Yani verilen path için bir fonksiyon atanıyor.
- `http.ListenAndServe(":3333", nil)` uygulamanın ayağa kalktığı kısım. Bir port bilgisi ve şimdilik `nil` olan bir parametre alıyor(bu kısma sonra değineceğiz).

Evet yukarıdaki kod parçasını çalıştırıp `http://localhost:3333/Mehmet` adresine gidersiniz karşınıza;

		Selamlar Mehmet !

çıkacaktır.

Bu hali ile pek RESTful API'den çok düz web sitesi gibi şuan. Şimdi uygulamamızı nasıl RESTful yaparız ona bakalım. Öncelikle dönen değeri nasıl `json` çeviririz öğrenelim. JSON işlemleri için Go'da built-in olarak gelen `encoding/json` kütüphanesini kullanacağız.

`Message` adında bir objemiz ve bu objenin text adında string bir alanı olsun. Ve bu objeyi servisten dönelim.

```go

package main

import (
	"encoding/json"
	"fmt"
	"net/http"
)

type Message struct {
	Text string
}

func rootHandler(rw http.ResponseWriter, req *http.Request) {
	message := Message{"Selamlar " + req.URL.Path[1:]}
	jsonMessage, err := json.Marshal(message)
	if err != nil {
		fmt.Fprintf(rw, "Bir hata oluştu")
	}
	fmt.Fprintf(rw, string(jsonMessage))
}

func main() {
	http.HandleFunc("/", rootHandler)
	http.ListenAndServe(":3333", nil)
}

```

Objeyi jsona çevirme işini `json.Marshal()` ile yapıyoruz. Aynı şekilde tam tersi olan yani jsonı objeye çevirme içinde `json.Unmarshal()` adında bir method bulunmakta.

Eğer şimdi çalıştırırsanız karşınza çıkacak mesaj:

		{
			Text: "Selamlar Mehmet"
		}

olacaktır.

JSON'dan dönen objenin fiedlarını değiştirmek istersek aşağıdaki işlemi uygulamamız yetecektir:

```go

type Message struct {
	Text string `json:"mesaj"`
}

```

Şimdi çıktımız:


		{
			mesaj: "Selamlar Mehmet"
		}

şeklinde olacaktır.

Başlamak için gerek bilgileri edindiğimize göre artık API'mizi yazmaya başlayabiliriz. Örnek olarak uygulmamız için `user` crud işlemlerini sağlayacak metodları yazalım.

