---
layout: post
title: "Cross-Origin Resource Sharing(CORS) Nedir ?"
date: 2016-02-13 20:55
comments: true
---
Çoğu developer gibi bende karşıma çıkan hataları çözmeye çalışırken yeni bişeyler öğreniyorum. Bir kaç gün önce cross-domain request'lerle ilgili çözmeye çalıştığım hata CORS mekanizmasını öğrenmeme vesile oldu. 
Hazır yeni bişeyler öğrenmişken diliğim döndüğüne buraya da yazıyım dedim.
CORS, web uygulamanıza kendi domaininiz haricinde dışardan gelen istekleri yönetmenizi sağlayan bir mekanizma. Ön tanımlı olarak sunucularda cross-domain AJAX request'leri, kötüye kullanılabileceği için, engellenmiştir.

Peki bildiğimiz bir domainden gelen request'leri almak istiyorsak ne yapacağız ? Bu soruya cevap vermeden önce CORS mekanizması nasıl çalışıyor ona bakalım.

Örnek senaryo üzerinden gitmek gerekirse, javascript ile bir AJAX requeste attığınızda browser eğer gerekli ise (ne zaman gerekli olduğunu sonra konuşacağız) önce bir Preflight request atar.
Preflight request'in cevabına göre de eğer sunucu izin veriyorsa asıl request'i atar. Buradan da anlaşılacağı gibi CORS aslında iki ayrı kısımdan oluşmakta. Browser ve server kısmı. Browser kısmından browserın geliştiricileri sorumlu,
server kısmndan ise biz. CORS mekanizması header'ları kullanarak çalışıyor. Request ve Response eklenen header'lar ile server ve client arasındaki yetki ve izin sorgusu yapılıyor. Şimdi bu header'ları inceleyelim.

## Request Headers:

### Origin

Request'in hangi domainde geldiğini belirtir. Ve her cross-domain request'te olması gereken header'dır.

		Origin: api.foo.com 

### Access-Control-Request-Method

Preflight request'i sırasında asıl request'in metodunu belirtir.(Preflight request'leri OPTIONS metodu ile yapılır.)

		Access-Control-Request-Method: PUT

### Access-Control-Request-Headers

Preflight request'i sırasında asıl request ile kullanılacak header'ları belirtir,

		Access-Control-Request-Headers: X-Custom-Header


##Response Headers:

### Access-Control-Allow-Origin

Server'ın izin verdiği domain(leri) içerir.

		Access-Control-Allow-Origin: api.foo.com

### Access-Control-Allow-Methods

Server'ın izin verdiği HTTP medolarını içerir.

		Access-Control-Allow-Methods: PUT

### Access-Control-Allow-Headers

Server'ın izin verdiği  header'ları içerir.

		Access-Control-Allow-Headers: X-Custom-Header

### Access-Control-Allow-Credentials

Bu header server'ın credentials(mesela cookie)'lere izin verip vermediği bilgisini içerir. 

NOT: Eğer request cookie eklemek istiyorsanız, Ajax requestine `withCredentials = true` property'sini eklemeniz gerek.

		Access-Control-Allow-Credentials: true

### Access-Control-Max-Age

Preflight requestleri maliyetli olduğu için browserda cache'lenir. Bu header da cache ne kadar süre geçerliği olduğu bilgisini döner.

		Access-Control-Max-Age: 3600



## Preflight Request Nedir ?

Preflight request yukarıda bashsettiğimiz gibi asıl atılması gereken request'ten önce atılan bir sorgulama requestidiri. OPTIONS metodu ile request atılır, dönen cevaba görede eğer server domaine izin veriyorsa ikinci asıl olan istek atılır. Simple request diye geçen istekler için gerekli değildir. "Peki simple request nedir kardeş ?" 

### Simplere Request

Bir request `simple` olabilmesi için bazı şartları sağlaması gerekiyor. O şartlarda şöyle:

Metod: `GET`, `HEAD`, `POST` 

Header: `Accept`, `Accept-Language`, `Content-Language`, `Content-Type` 

NOT: `Content-type` headerıda sadece `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` değerlerini alabilir.

Yukarıdaki şartları sağlayan istek için preflight request gerekmez.

## CORS İçin Server Yapılandırma

Server'ınızın ya da uygulamanızın cross-domain isteklere cevap vermesi için bir takım ayarlamalar yapamınız gerekmekte. Yapılandırma işini uygulamanızın içinde kodlama yoluyla yapabileceğiniz gibi, web server'ınızı yapılandırarak da halledebilirsiniz. [Burada](http://enable-cors.org/server.html) çeşitli platformlarda nasıl yapılacağını gösteren basit ve anlaşılır bir kaynak mevcut.

Ben sadece `spring-framework` kullananalar için nasıl yapıldığını göstermekle yetineceğim.

```java
	@Configuration
    @EnableWebMvc
    public class WebConfig extends WebMvcConfigurerAdapter {

		@Override
		public void addCorsMappings(CorsRegistry registry) {
			registry.addMapping("/api/**")
				.allowedOrigins("http://foo.com")
				.allowedMethods("PUT", "DELETE");
		}
	}
```

Çok basit şekilde yukarıdaki gibi bir yapılandırma ile foo.com'dan /api/ ile başlayan resource'larınıza gelen PUT ve DELETE metodları içeren requestlere izin vermiş oluyorsunuz.






