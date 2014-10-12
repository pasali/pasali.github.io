---
layout: post
title: "Debian/Ubuntu Golang Kurulumu"
date: 2013-10-05 17:15
comments: true
categories:
---
Sisteminize uygun sürümü [indirin](http://code.google.com/p/go/downloads/list)
Eğer önceden kurulum yaptıysanız aşağıdaki komutu çalıştırın <!-- more -->

        $ rm -r /usr/local/go

Sıfır kurulum için indirdiğiniz arşivi açın

        $ tar -C /usr/local -xzf go1.1.linux-xxx.tar.gz 

PATH değişkenine go’yu ekleyin

        $ export PATH=$PATH:/usr/local/go/bin  

Aşağıdaki kod parçasını bir text dosyasına yazıp adını ilk.fo diye kaydedin.

```go
package main

import "fmt"

func main {
        fmt.Println("Selamun Aleyküm")
}
```
Deneme sürüşü

        $ go run ilk.fo
