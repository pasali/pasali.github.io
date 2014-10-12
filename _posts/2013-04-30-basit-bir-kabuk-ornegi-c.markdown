---
layout: post
title: "Basit Bir Shell - C"
date: 2013-04-30 18:01
comments: true
categories:
---

İşletim Sistemleri dersinde C programlama dilinde basit bir **kabuk(shell)** kodlanmamız istendi. Hocam( [Recia Oktaş](http://roktas.me/) ) yardımıyla basit bir kabuk oluşturdum. Burada size dilim döndüğünce nasıl yaptığımı anlatacağım.
<!-- more -->
#### fork() - exec()
Ödevin asıl amacı fork-exec mekanizmasını kavramamızdı. Kodları anlayabilmeniz için fork-exec mekanizmasının nasıl çalıştığını bilmeniz gerekiyor. Kabaca `fork()`, çalışan prosesin bir kopyasını oluştur ve `fork()`'un bulunduğu satırdan sonra iki farklı proses çalışmaya başlar. `exec()` ise kopyalanan prosese başka bir programı yerleştirmeye yarar.

  Bu bilgiden sonra kodlamaya dönebiliriz. Öncelikle yazacağımız fonksiyonları tanıyalım.

``` c
    void   argv_free(void);
    int    run(void);
    int    parse(char *);
    void   command_cd(void);
    void   command_exit(void);
    struct command_t find_command(char *);
```

`argv_free()` fonksiyonu adından da anlaşılacağı gibi `malloc()` ile ayırdığımız bellek alanlarını boşaltıyor. `run()` fonksiyonu çalışması istenilen programı çalıştırıyor. `parse()` ise string şeklinde aldığımız komut ve argümanlarını istediğimiz biçime dönüştürmemizi sağlıyor. `command_cd()` ise `cd` komutunun gerçeklemesi. `command_exit()` ise programın çıkacağı durumlarda devreye giriyor. `find_command()` ise `exec()` ile çalıştıramadığımız `built-in` komutları oluşturduğumuz tabloda arayıp buluyor ve döndürüyor.

  Artık tek tek fonksiyonları inceleyebiliriz.


``` c
    void
    argv_free(void)
    {
            int i;

            for (i = 0;  arguments[i]; i++) {
                free(arguments[i]);
            }
    }
```
`arguments` adında bir dizinin( komut ve argumanları tuttuğumuz dizi ) elemanlarının bellekte tuttuğu alanları boşaltıyor.


``` c
    int
    run(void)
    {
            int status;
            pid_t pid;
            pid = fork();

            if (!pid) {
                    int ret;
                    if ( **arguments == '/')
                            ret = execv(arguments[0], arguments);
                    else
                            ret = execvp(arguments[0], arguments);
                    if (ret == -1) {
                            perror("execv");
                            exit(EXIT_FAILURE);
                    }
            } else if (pid == -1)
                    perror("fork");

            if (waitpid(-1, &status, 0) == -1) {
                    perror("waitpid");
                    exit(EXIT_FAILURE);
            }

            return 0;
    }
```
`run()` ise yukarda belirttiğimiz gibi çalıştırma işlemini gerçekliyor.
Bunu yaparken `fork()` ile bir kopya proses oluşturuyor önce, sonra iki alernatifimiz var `execv()` ve `execvp()`.
`execv` tam dosya yolu ile çalşıyor yani komut satırından `/bin/ls` gibi bir komut geldiğinde `execv` devreye giriyor.
`execvp` ise verilen komutu `PATH` te aratıyor. Yani `ls` diye bir komut geldiğinde bu sefer devreye `execvp` giriyor.
2.argüman olarak ikiside komutlardan ve argümanlardan oluşan bir dizi alıyor.(`arguments`)
`waitpid`  ise kabaca kopyalanan prosesin sonlandığından emin olmamızı sağlıyor.

```c
    int
    parse(char *string)
    {
            char ** str_p = &string;
            char * token;
            int len;
            struct command_t ret;

            add_history(string);
            for (len = 0; (token = strsep(str_p, " \t")); len++) {
                    arguments[len] = strdup(token);
            }
            arguments[len] = NULL;
            ret = find_command(arguments[0]);
            if (ret.command != NULL) {
                    ret.handler();
            }
            if (komut[0] != '/' && strchr(komut, '/')) {
                    printf("Sadece program adı ya da tam yolu girin !!!\n");
                    main();
            }

            return 0;
    }
```
`parse()` fonksiyonu komut satırından aldığımız `ls -l /home` gibi bir stringi bize `["ls", "-l", "/home"]` şeklinde geri döndürüyor.
Bunu yapmamızın nedeni ise `execv` ve `execvp` argüman dizisi ile çalışıyor olması. Aslında buradaki problem basit bir stringi boşluklara göre
böl problemi. Bunu çözerken `strsep` adında bir hazır fonksiyon kullandım. Eğer program bir `built-in` fonksiyonsa daha önceden oluşturduğumuz tablodan bakıp komutu çalıştırıyor.
`add_history` bize komut geçmişi yaratmamızı sağlıyor. Komut ekranında beklerken yön tuşları ile daha önceki komutları erişebiliyoruz.
Burada bir de kontrol işlemi yapıyoruz. Girilen komut `bin/ls`  ve ya türevleri şeklindeyse bir uyarı verip tekrar komut istiyoruz.

``` c
    void
    command_cd(void)
    {
            if (!strcmp(arguments[1], "~") || !strcmp(arguments[1], " ") || !strcmp(arguments[1], "")) {
                    chdir(getenv("HOME"));
            } else if (!strcmp(arguments[1], ".")) {
                    /* none */
            } else if (!strcmp(arguments[1], "..")) {
                    char * parent_directory = strrchr(getenv("PWD"), '/');
                    char * pwd = getenv("PWD");
                    int index = parent_directory - pwd;
                    pwd[index] = '\0';
                    chdir(pwd);
            } else
                    chdir(arguments[1]);
            main();
    }
```
Basit bir `cd` programının gerçeklemesi. İlk `if` ifadesinde `home` dizinine gideceğimiz durumları kontrol ediyoruz. `chdir()` fonksiyonu için
anlamsız olan ~ ve boşluk karakterleri gelince ne yapacağını söylüyoruz. İkininci blokta ise linux `bulunduğumuz dizin` anlamına gelen `.` karaterini soyutluyoruz. Üçüncü blokta ise `bir üst dizin` anlamına gelen `..` karaterini soyutluyoruz. Bunu yaparken `strrchr` fonskiyonunda yardım alıyoruz. Bu fonksiyon  verilen karakteri string içinde arıyor ve en son bulduğu indisten sonraki kısmını döndürüyor. Yani `/home/mhmt/Desktop` için
`/Desktop` döndürecektir. Bu durumda `parent_directory` `/Desktop` olacaktır. `pwd` ise `/home/mhmt/Desktop` değerini tutacaktır. Pointer aritmetiği sayesinde `index` yardımı ile son `/` karakterinin yerini buluyoruz ve sonraki kısmı atıyoruz. Böylece bir üst dizinin yoluna ulaşmış olduk.
`else` ile verilen dizin yoluna geçiş yapıyoruz.

``` c
    void
    command_exit(void)
    {
            exit(EXIT_SUCCESS);
    }
```
Anlayacağınız üzere  programdan çıkmamızı sağlıyor.

``` c
    struct command_t
    find_command(char * cmd)
    {
            int i;

            for (i = 0; i < 3 ; i++) {
                    if (!strcmp(cmd, commands[i].command)) {
                            return commands[i];
                    }
            }
            return commands[i];
    }

```
`find_command` fonksiyonu aşığadaki tablo da daha önceden yazdığımız `built-in` komutlarda arama yapıp varsa komutu ilgili fonksiyona döndürüyor.

``` c
    struct command_t {
            char *command;
            void (*handler) (void);
        } commands[] = {
            {"cd", &command_cd},
            {"quit", &command_exit},
            {"exit", &command_exit},
            {NULL, NULL}
    };
```
Komut ismi ve ilgili fonksiyonun pointerindan  oluşan bir `command_t` veri tipimiz var. Hemen sonrasında ise bu veri tipi kullanılarak oluşturulmuş bir
`commnands` dizisi var. Burada tablo yönemli programlama kullanmamızın nedeni gereksiz `if - else` bloklarından kaçınmak için.  
``` c
    int
    main(void)
    {
            for ( ; ; ) {
                    komut = readline("#> ");
                    if (komut == NULL) {
                            exit(EXIT_SUCCESS);
                    } else if (!strcmp(komut, "")) {
                            continue;
                    } else {
                            parse(komut);
                            run();
                            argv_free();
                    }
                    free(komut);
            }
            atexit(argv_free);

            return 0;
    }
```
Ve `main()` fonksiyonumuz. `readline` ile komutumuzu okuyoruz. komut NULL ise (yani ctrl - d ) programdan çıkıyoruz. Boş ise tekrar komut istiyoruz. Ve diğer durumda fonksiyonlarımızı çaığırıyoruz. `atexit` le ayırdığımız bellek alanlarının boşaltındığından emin oluyoruz.  

Kodun tamamına [burdan](https://github.com/pasali/shellim/blob/master/shellim.c) ulaşabilirsiniz.
