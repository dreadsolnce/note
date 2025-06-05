
## Основные команды

***Настройка nginx:***
1. Основной каталог для сайтов /var/www/html
> [!important] Важно
> **Из других каталогов может не запуститься**

2. Изменение владельца запуска службы nginx:
```
sudo vim /etc/nginx/nginx.conf
	параметр: user
```

3. Для проверки можно попробовать получить статистику каталога от того пользователя под которым запущен сервер nginx и если будет **permission denied**, то таким образом следует, что nginx не может открыть файлы сайта.

4. Лучшим решением в этом случае будет добавить `www-data`в `username`группу:
```
gpasswd -a www-data username
```
   a) убедитесь, что `username`группа может войти во все каталоги по пути:
```
chmod g+x /home && chmod g+x /home/kvl && chmod g+x /home/kvl/html_css
```

***Проверка конфигурации:***

```
sudo nginx -t
```

***Безопасная перезагрузка службы***

```
sudo nginx -s reload
```


## Директива server_name \_;

> [!NOTE] Описание
> `server_name _` - используется для обозначения любого имени хоста, которое не совпадает ни с одним из явно указанных имён серверов

### Пример:

![[Screenshot 2025-06-03 144859.png]]
В этом примере, первый блок сервера обрабатывает запросы к домену `example.com`. 
Второй блок сервера с `server_name _` будет обрабатывать любые другие запросы, не соответствующие первому
## Описание параметров

#### 1. Переход в другой location

##### 1.1 Перенаправление в другой блок выполняется за счет одной из приведенных ниже директив:

1. **try_files**
2. **rewrite**
3. **error_page**

	***Пример 1:***
		
		location / {
			try_files $uri $uri.html $uri/ /none/index.html;
		}
		location /none {
			root /var/sites/new
		}
	При поступлении запроса к example.com/request Nginx первым делом увидит **$uri** и попытается найти location request. Такого нет, далее следует поиск _request.**html**_ и каталога _request_. Если таких нет, то запрос переадресуется в резервный _location_ на страницу _/none/index.html_. Веб-сервер выдаст пользователю содержимое _/var/sites/new/none/index.html_

	***Пример 2:***
		
		server_name www.firesdash.test.local firesdash.test.local
		location / {
			root /var/www/html/fron/firesdash.ru/dist;
			index index.html;
			try_files $uri /index.html;
		}
	При поступлении запроса к example.com/request Nginx первым делом увидит **$uri** и попытается найти location request. Такого нет и веб сервер откроет /var/www/html/fron/firesdash.ru/dist/**index.html**. А при поступлении запроса firesdash.test.local (www.firesdash.test.local) Nginx открывает **index** либо **index.html**

	***Пример 3:***
		
		server_name www.firesdash.test.local firesdash.test.local
		location / {
			root /var/www/html/fron/firesdash.ru/dist;
			index index.html;
			try_files $uri $uri/ =404;
		}
	При поступлении запроса к example.com/request Nginx первым делом увидит **$uri** и попытается найти location request. Такого нет и веб сервер откроет вернёт ошибку 404

#### 2. Отключение кэширования

```ruby
add_header Cache-Control 'no-store, no-cache, must-revalidate, proxy-revalidate, max-age=0';
```

**no-store** - не кэшировать ответ.
**no-cache** - Это фактически позволяет кэшировать ответ, хотя он немедленно помечается как устаревший и всегда повторно проверяется исходным сервером.
**must-revalidate** - означает только, что устаревший кэшированный документ должен быть повторно проверен исходным сервером.
**proxy-revalidate** - аналогично must-revalidate, за исключением того, что применяется только к кэшам прокси.

#### 3. Кэширование статических файлов

```
# assets, media
location ~* \.(?:css(\.map)?|js(\.map)?|jpe?g|png|gif|ico|cur|heic|webp|tiff?|mp3|m4a|aac|ogg|midi?|wav|mp4|mov|webm|mpe?g|avi|ogv|flv|wmv|geojson)$ {
	expires	7d;
	access_log on;
}

#svg, fonts
location ~* \.(?:svgz?|ttf|ttc|otf|eot|woff2?)$ {
	add_header Access-Control-Allow-Origin "*";
	expires	7d;
	access_log on;
}

```
> Обратите внимание на то, что nginx не отдает заголовки Expires. Если для сгенерированных интерпретатором PHP документов это не всегда важно, то статические данные желательно отдавать с подобным заголовком, позволяя броузерам посетителей более активно использовать кэширование. В данном конфигурационном блоке есть раздел location для обработки статических докуменов. Именно в этом блоке добавьте строку expires Xd, где Х — количество дней валидности данных. Например, expires 7d укажет броузеру, что изображения и файлы CSS/JS можно кэшировать на протяжении недели.


#### 4. Настройка php-fpm

![[Снимок экрана от 2025-04-11 11-54-24.png]]
> Все файлы в запросе (http://localhost/api.php) оканчивающиеся на .php будут запускаться (выполняться), а не загружаться.

#### 5. Пропуск favicon.ico

```python
# skip favicon.ico
#
location = /favicon.ico {
    access_log off;
    return 204;
}
```

## Настройка nginx в качестве балансировщика

[Ссылка на статью habr.com](https://habr.com/ru/companies/first/articles/683870/)

- ***Создаём новый файл конфигурации для балансировщика нагрузки***
```
cd /etc/nginx/sites-available/ ; sudo vim proxy_to_srv
```

Содержимое файла:
```
upstream proxy_to_srv {
    server 192.168.192.101:80;
}

upstream proxy_to_landing {
    server 192.168.192.101:8080;
}

upstream proxy_to_default {
    server 192.168.192.101:8000;
}

# Этот сервер принимает весь трафик на порт 80 и передает его вышестоящему потоку.
# Обратите внимание, что имя вышестоящего потока и proxy_pass должны совпадать.

server {
    listen 80;
  
    server_name www.firesdash.test.local firesdash.test.local;
  
    location / {
        include proxy_params;
      
        proxy_pass http://proxy_to_srv;
      
        proxy_redirect off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}

server {
    listen 80;
  
    server_name www.landing.test.local landing.test.local;
  
    location / {
        include proxy_params;
      
        proxy_pass http://proxy_to_landing;
      
        proxy_redirect off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}

server {
    listen 80 default_server;
    #   server_name default_server; 
    #   server_name www.landing.test.local landing.test.local;
  
    location / {
        include proxy_params;
      
        proxy_pass http://proxy_to_default;
      
        proxy_redirect off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}

```

> [!NOTE] Описание
> _**Upstream** определяет, куда Nginx будет передавать запросы после их получения_
> _**listen** определяет порт 80, через который Nginx будет получать запросы_
> _**proxy_pass** используется для указания NGINX, _куда отправлять получаемый трафик_
> _**server_name \_** используется для обозначения любого имени хоста, которое не совпадает ни с одним из явно указанных имён серверов_
> <h5>Общее описание</h5>
> nginx принимает запросы на 80 порту для трёх сайтов, но в зависимости от server_name<br>
> (адреса введенного в url) он отправаляет на один из трех блоков.<br>
> <hr>
> 
> **Например**: если ввести в адресную строку http://firesdash.test,local он отправит в первую
> секцию, т.к. server_name = firesdash.test.local, но если ввести в адресную строку
> http://firesdash он отправит уже в секцию по умолчанию, которая определяется в третьем
> блоке, а именно listen 80 **default_server;**
> 






## Кракозябры при отображении страницы:

Решение:

![[Снимок экрана от 2025-06-02 15-45-19.png]]

## Примеры 

- #### Одновременный запуск http и https

```perl
Файл /etc/nginx/sites-available/default

server {
	listen 80 ;
	listen [::] ;
	root /var/www/html;
	index index.html index.htm index.nginx-debian.html;
	server_name firesdash.ru www.firesdash.ru;
	location{
		try_files $uri &uri/ =404;
	}
}
server {
 	listen 443 ssl;
 	listen [::]443 ssl;
 	server_name firesdash.ru www.firesdash.ru;
 	root /var/www/html;
 	index index.html index.htm index.nginx-debian.html;
 	ssl_certificate /etc/ssl/firesdash.ru.cert
 	ssl_certificate_key /etc/ssl/firesdash.ru.key
 	location{
 		try_files $uri &uri/ =404;
 	}
 }
```

