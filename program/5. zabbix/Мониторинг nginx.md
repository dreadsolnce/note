***Проверяем на клиенте установлен ли необходимый модуль для работы:***

```
sudo nginx -V 2>&1 | grep -o with-http_stub_status_module
```

![[Снимок экрана от 2026-02-10 18-35-39.png]]

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