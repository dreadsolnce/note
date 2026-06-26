***Проверяем на клиенте установлен ли необходимый модуль для работы:***

```
sudo nginx -V 2>&1 | grep -o with-http_stub_status_module
```

![[Снимок экрана от 2026-02-10 18-35-39.png]]

**1 ВАРИАНТ:**
***Создаем конфигурационный файл /etc/nginx/conf.d/stub_status.conf***

server {
    listen 127.0.0.1:8900;
    server_name 127.0.0.1;
	
    location = /basic_status {
       stub_status;
       access_log off;
       allow 127.0.0.1;
       deny all;
    }
}

![[Снимок экрана от 2026-02-10 18-39-07.png]]

**2 ВАРИАНТ:**
******Создаем конфигурационный файл /etc/nginx/sites-enabled/default_server.conf***

```
cat sites-enabled/default_server.conf 
###redirect !https
server {
  http2 on;
  listen 80 default_server;
  listen 443 ssl default_server;
  
  server_name _;
 
   # SSL
  include ./snippets/ssl.conf;
  include ./snippets/ssl-wild.conf;

  #  location /server-status {
  #    proxy_pass http://127.0.0.1:8080;
  #  }

  location = /basic_status {
    stub_status;
    allow 127.0.0.1;
    allow 172.16.126.9;
    allow ::1;
    deny all;
  }
  
  location / {
      return 503;
  }
}
```

![[Снимок экрана от 2026-05-27 12-07-19.png]]

***Проверяем конфигурацию и пере запускаем nginx***

```
sudo nginx -t
```

```
sudo nginx -s reload && sudo ss -tlpn | grep 8900
```

___

***На сервере zabbix выполняем следующее:***

![[Снимок экрана от 2026-02-11 10-21-20.png]]

![[Снимок экрана от 2026-02-11 10-17-40.png]]

```
{$NGINX.STUB_STATUS.HOST}   :   127.0.0.1
{$NGINX.STUB_STATUS.PORT}   :   8900
```

***Проверяем***

![[Снимок экрана от 2026-02-11 10-25-37.png]]

***Назад:*** [[program/5. zabbix/!-----Содержание-----!|!-----Содержание-----!]]