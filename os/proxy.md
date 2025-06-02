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

