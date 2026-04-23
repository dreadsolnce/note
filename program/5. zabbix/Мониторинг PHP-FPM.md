***Находим конфигурационный файл***

```
sudo find /etc/ -name www.conf 2> /dev/null
```

***Открываем параметры для доступа к статистике***

```
sudo vim /etc/php/8.4/fpm/pool.d/www.conf
```

```
pm.status_path = /status
ping.path = /ping
```

***Редактируем файл nginx, добавив следующую location***

```
sudo vim /etc/nginx/sites-enabled/default_server.conf
```

```
location ~ ^/(status|ping)$ {
    access_log off;
    allow 127.0.0.1;
    deny all;
    
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_index index.php;
    include fastcgi_params;
    fastcgi_pass unix:/run/php/php8.4-fpm.sock;
  }
```

***Проверяем конфигурацию***

```
sudo php-fpm8.4 -t
```

***Перезапускаем службу***

- более безопасный вариант:

```
sudo systemctl reload php8.4-fpm.service
```

- полная перезагрузка службы

```
sudo systemctl restart php8.4-fpm.service
```

```
sudo systemctl status php-fpm8.4.service
```

***Проверяем конфигурацию nginx***

```
sudo nginx -t
```

***Перезапускаем службу***

```
sudo nginx -s reload
```

***Добавляем шаблон на сервер zabbix***

*Configuration -> Hosts -> Host -> Select -> Templates/PHP-FPM by Zabbix agent*

### Общий файл конфигурации nginx (default_server):

```
###redirect !https
server {
  http2 on;
  listen 80 default_server;
  #listen 443 ssl default_server;
  
  server_name _;
 
   # SSL
  #include ./snippets/ssl.conf;
  #include ./snippets/ssl-wild.conf;

  location = /basic_status {
    stub_status;
    allow 127.0.0.1;
    allow 172.16.126.9;
    allow ::1;
    deny all;
  }

  location ~ ^/(status|ping)$ {
    access_log off;
    allow 127.0.0.1;
    deny all;
    
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_index index.php;
    include fastcgi_params;
    fastcgi_pass unix:/run/php/php8.4-fpm.sock;
  }
  
  location / {
      return 503;
  }
}

```

