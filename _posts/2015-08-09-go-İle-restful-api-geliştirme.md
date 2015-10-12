---
layout: post
title: "Go İle RESTful API Geliştirme"
date: 2015-08-09 19:32
comments: true
---
Başlıktan da anlaşılacağı gibi Go ile basit bir API yazacağız. Yazacağımız API'nin çok fazla fonksiyonu olmayacak sadece başlamak için gerekli bilgileri içerecek. Geri kalan kısmı ise istediğiniz gibi kendiniz yazabileceksiniz. Çalışma ortamınızda Go'nun kurulu olduğunu ve bir Go programını nasıl çalıştırmanız gerektiğini bildiğiniz varsayıyorum. Eğer halihazırda kurulu değilse google'da küçük bir arama ile gerekli kurulumları yapabilirsiniz.

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

`Message` adında bir veri yapımız olsun ve servisten dönelim.

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

Struct'ı JSON'a çevirme işini `json.Marshal()` ile yapıyoruz. Aynı şekilde tam tersi olan yani JSON'ı Struct'a çevirme içinde `json.Unmarshal()` adında bir method bulunmakta.

Eğer şimdi çalıştırırsanız karşınza çıkacak mesaj:

		{
			Text: "Selamlar Mehmet"
		}

olacaktır.

JSON'dan dönen struct'ın fiedlarını değiştirmek istersek aşağıdaki işlemi uygulamamız yetecektir:

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

Başlamak için gerek bilgileri edindiğimize göre artık API'mizi yazmaya başlayabiliriz. Örnek API'miz sadece kullanıcı CRUD işlemlerini gerçekleştirsin.

API'mizin veritabanı bağlantısı olamayacak, veritabanı yerine bir map kullanarak veri saklama işini halledeceğiz. Veritabanı olayı anlatacaklarımdan bağımsız şekilde öğrenilebilir.

Şimdi bir kullanıcı veriyapısı oluşturalım:

```go

type User struct {
	FirstName string `json:"firstName"`
	LastName  string `json:"lastName"`
	Email     string `json:"email"`
}

```

Çok basit hali ile isim, soyisim ve e-posta bilgilerini içeren bir User Struct'ımız var.

Şimdi API'mizi kullanacaklar için kullanıcı oluşturabilecekleri bir endpoint yaratalım. "/users" CRUD işlemlerini gerçekleştireceğimiz pathimiz olsun. Eğer bu path'e POST ile anlamlı bir JSON gelirse map'imize yeni bir kullancı ekleyelim. 

Kullanıcı yaratma işlemi için `createHandler` adında yeni bir fonksiyon yazalım:

```go

func createHandler(w http.ResponseWriter, r *http.Request) {
	body, err := ioutil.ReadAll(r.Body)
	if err != nil {
		panic(err)
	}
	if err := r.Body.Close(); err != nil {
		panic(err)
	}
	var user User
	w.Header().Set("Content-Type", "application/json; charset=UTF-8")
	if err := json.Unmarshal(body, &user); err != nil {
		w.WriteHeader(http.StatusBadRequest)
		if err := json.NewEncoder(w).Encode("HATA"); err != nil {
			panic(err)
		}
	}
	users[user.Email] = user
	w.WriteHeader(http.StatusCreated)
	if err := json.NewEncoder(w).Encode(user); err != nil {
			panic(err)
	}
}

```

Yukarıdaki kod parçası basit olarak, gelen request'in içinden veriyi okuyup body isimli değişkene atıyor. Eğer bir hata oluşursa hatayı verip programdan çıkıyor. Sonraki adımda request'in içini okumak için kullandığı stream'i kapatıyor. Ve yine hata durumunu kontrol ediyor. Sonraki if bloğunda ise gelen JSON veriyi Struct'a çeviriyor. İşlem esnasında hata olursa 422 nolu HTTP hata kodu ile beraber 'HATA' mesajı dönüyor.

Şimdi main fonksiyonumuzu aşağıdaki gibi güncelleyelim:

```go

func main() {
	http.HandleFunc("/users", createHandler)
	http.HandleFunc("/", rootHandler)
	http.ListenAndServe(":3333", nil)
}

```

Uygulamayı çalıştırıp, "localhost:3333/users" adresine aşağıdaki gibi bir JSON yollarsanız 201 HTTP kodu ile oluşturduğunuz user'ı dönecektir.

		{
 			"firstName": "Mehmet",
			"lastName": "Başal",
 			"email": "mhmtbsl@gmail.com"
		}

Artık updateHandler, deleteHandler ve getHandler işlevlerinide yazabiliriz ve `rootHandler` fonksiyonuna ihtiyacımız yok, kaldırabiliriz.

`http.ListenAndServe(":3333", nil)` => buradaki nil parametresi, Go'ya onun default ServeMux'unu kullanmak istiyoruz demek. Go'nın bult-in ServeMux çok esnek ve kullanışlı değil. Yani "/users/1" gibi bir URL oluşturmak isterseniz third-party bir kutuphane kullanmanız gerekiyor. Bizde şuan en çok kullanılan Gorilla projesinin Mux kütüphanesini kullanacağız. Kullanımı oldukça basit ve anlaşılır.

RESTful web servislerinde bilindiği üzere her endpoint sunucuda bir resource denk gelmek zorundadır. Resource ise kendine özgü metodları olan bir veri yapısı olarak düşünülebilir. Bu resource'lar için tanımlı standart metodlar tanımlanmış bulunmakta bunlar sırasıyla; GET, POST, PUT ve DELETE. Bizim tek resource'umuz User olduğuna göre enpointlerimizi buna göre tasarlamaya başlayabiliriz.

`/users` => Bu endpoint'e POST methodu ile gelecek anlamlı JSON'lar  sunucuda bir User oluşacak.

`/users/{email}` => Bu endpoint'e GET ile istek yapıldığnda sunucudan verilen email'e sahip User dönecek.

`/users/{email}` => Bu endpoint'e DELETE ile istek yapıldığnda sunucudan verilen email'e sahip Usersilinecek.

`/users/{email}` => Bu endpoint'e PUT ile istek yapıldığnda sunucudan verilen email'e sahip User'ın bilgileri güncellencek.


Şimdi route'larımızı oluşturalım bunun için önce Gorilla kütüphanesini indirmemiz lazım.

		go get github.com/gorilla/mux

ile kolayca indirebilirsiniz. Sonrada import edip projede kullanmaya başlayabilirisiniz.

`main` fonksiyonunu aşağıdaki gibi düzenleyelim. Yukarıdaki anlatıklarımın kodu dökülmüş hali gibi düşünebilirsiniz. Dikkat etiyseniz artık ListenAmdServe metoduna `nil`
değil, oluşturduğumuz router objesini veriyoruz.

```go

func main() {
	router := mux.NewRouter().StrictSlash(true)
	router.Methods("POST").Path("/users").HandlerFunc(createHandler)
	router.Methods("GET").Path("/users/{email}").HandlerFunc(getHandler)
	router.Methods("DELETE").Path("/users/{email}").HandlerFunc(deleteHandler)

	http.ListenAndServe(":3333", router)
}

```

`UPDATE` metodunu yazmayacağım onu sizde sonra yazabilirsiniz :)

Biz şimdilik deleteHandler fonksiyonunu yazalım. Aslında verilen key için karşılığını map'ten sileme işini yapacak. Çok basit hali ile:

```go

func deleteHandler(w http.ResponseWriter, r *http.Request) {
	w.WriteHeader(http.StatusOK)
	vars := mux.Vars(r)
	email := vars["email"]
	delete(users, email)
	json.NewEncoder(w).Encode(email)
}

```

Aynı şekilde  getHandler fonksiyonu yazalım:

```go

func getHandler(w http.ResponseWriter, r *http.Request) {
	w.WriteHeader(http.StatusOK)
	vars := mux.Vars(r)
	email := vars["email"]
	json.NewEncoder(w).Encode(users[email])
}

```

Son olarak kodların hepsi aşağıda:

```go

package main

import (
	"encoding/json"
	"github.com/gorilla/mux"
	"io/ioutil"
	"net/http"
)

var users map[string]User = make(map[string]User)

type User struct {
	FirstName string `json:"firstName"`
	LastName  string `json:"lastName"`
	Email     string `json:"email"`
}

func createHandler(w http.ResponseWriter, r *http.Request) {
	body, err := ioutil.ReadAll(r.Body)
	if err != nil {
		panic(err)
	}
	if err := r.Body.Close(); err != nil {
		panic(err)
	}
	var user User
	w.Header().Set("Content-Type", "application/json; charset=UTF-8")
	if err := json.Unmarshal(body, &user); err != nil {
		w.WriteHeader(http.StatusBadRequest)
		if err := json.NewEncoder(w).Encode("HATA"); err != nil {
			panic(err)
		}
	}
	users[user.Email] = user
	w.WriteHeader(http.StatusCreated)
	if err := json.NewEncoder(w).Encode(user); err != nil {
		panic(err)
	}
}

func getHandler(w http.ResponseWriter, r *http.Request) {
	w.WriteHeader(http.StatusOK)
	vars := mux.Vars(r)
	email := vars["email"]
	json.NewEncoder(w).Encode(users[email])
}

func deleteHandler(w http.ResponseWriter, r *http.Request) {
	w.WriteHeader(http.StatusOK)
	vars := mux.Vars(r)
	email := vars["email"]
	delete(users, email)
	json.NewEncoder(w).Encode(email)
}


func main() {
	router := mux.NewRouter().StrictSlash(true)
	router.Methods("POST").Path("/users").HandlerFunc(createHandler)
	router.Methods("GET").Path("/users/{email}").HandlerFunc(getHandler)
	router.Methods("DELETE").Path("/users/{email}").HandlerFunc(deleteHandler)

	http.ListenAndServe(":3333", router)
}


```

Şuan servisim hazır çalıştırıp deneme sürüşüne çıkabilirsiniz. :)



