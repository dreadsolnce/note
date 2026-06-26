Для менеджера пакетов apt необходимо в файле /etc/apt/apt.conf.d/60-proxy прописать следующие строки:

```
sudo vim /etc/apt/apt.conf.d/60-proxy
```

```
Acquire::http::proxy "http://192.168.2.200:3128/";
Acquire::https::proxy "http://192.168.2.200:3128/";
```

Авторизация:

```
Acquire::http::proxy "http://user:p4ssw0rd@192.168.2.200:3128/";
Acquire::https::proxy "http://user:p4ssw0rd@192.168.2.200:3128/";
```

