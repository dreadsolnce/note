<h3>Использование прокси:</h3>
[Ссылка](https://pc.ru/articles/nastrojka-proksi-v-apt)

Для менеджера пакетов apt необходимо в файле /etc/apt/apt.conf прописать следующие строки:

```
Acquire::http::proxy "http://192.168.2.200:3128/";
Acquire::https::proxy "http://192.168.2.200:3128/";
```

***С помощью программы curl:***

&&&

<h1>Установка proxy squid</h1>
[youtube](https://www.youtube.com/watch?v=g4WhSBjIijg&t=904s)
[статья](https://blog.sedicomm.com/2019/04/05/kak-ustanovit-i-nastroit-chastnyj-anonimnyj-proksi-server-squid-na-baze-linux-ubuntu-debian/)

```
sudo apt update
```

```
sudo apt upgrade
```

```
sudo apt install squid apache2-utils
```

```
cp squid.conf squid.conf,default
```

```
> squid.conf
```

внести данные см. [статья](https://blog.sedicomm.com/2019/04/05/kak-ustanovit-i-nastroit-chastnyj-anonimnyj-proksi-server-squid-na-baze-linux-ubuntu-debian/) 

```
touch KnownUsers.acl
```
	+ 212.23.211.120

```
touch passwd
```

```
htpasswd /etc/squid/passwd kvl
```

<h4>Настройка https</h4>
sudo apt install squid-openssl


<h4>Настройка безопасности</h4>
<h5>Установка opendns:</h5>
www.opendns.com

Залогиниться -> dreadsolnce@gmail.com - пароль длинный

Ввести ip адрес сервера, к которому будут применены настройки dns

![[Screenshot 2025-06-02 112700.png]]


<h4>Настройка pi-hole.net</h4>

Блокировщик рекламы

```
curl -sSL https://install.pi-hole.net | bash
```

Удаление pi-hole:

```
sudo pihole uninstall
```

