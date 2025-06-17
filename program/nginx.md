
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


## Настройка основного конфигурационного файла

Узнаем количество процессоров в системе

`cat /proc/cpuinfo | grep processor | wc -l` или `nproc`

Заменить параметр worker_process на количество процессов

**worker_processes 1;**

**worker_rlimit_nofile 100000;**
Увеличивает лимит числа открытых файловых дескрипторов (FD). Если у NGINX будет много одновременных соединений, он сможет обслуживать их без ошибок вида "too many open files"

events {
	**worker_connections 4000;**
}
worker_connections задаёт, сколько одновременно входящих соединений (входящих запросов) может обрабатывать каждый worker. Увеличение этого значения даёт возможность обслуживать больше клиентов параллельно. Браузер одного клиента может открывать больше одного соединения. Чтобы понять какое число соединений может обработать процессор можно выполнить команду `ulimit -n` и стоит указать это число.

events {
	**use epoll;**
}
Epoll - высокопроизводительный механизм обработки событий в Linux. Улучшает масштабируемость и скорость реакции при большом числе одновременных соединений.

events {
	**multi_accept on;**
}
Разрешает worker-процессу принимать несколько новых соединений за один вызов. Ускоряет приём входящих соединений и снижает нагрузку на CPU при большом потоке запросов.

http {
**open_file_cache max=200000 inactive=20s;**
Включает кэширование дескрипторов открытых файлов и метаданных. Ускоряет доступ к часто запрашиваемым файлам, так как при повторном запросе не нужно повторно открывать файл с нуля

**open_file_cache_valid 30s; open_file_cache_min_uses 2; open_file_cache_errors on;**
Настройки для кэша открытых файлов: интервал проверки кэша (30s), минимальное число обращений к файлу, чтобы он оставался в кэше (2), и логирование ошибок при работе с кэшем (on). Все эти параметры помогают оптимизировать затраты га открытие файлов при нагруженных серверах.

**access_log off;**
Отключение журнала доступа снижает нагрузку на файловую систему и процессор, так как не нужно каждый запрос записывать в лог. Однако при отладке и боевой эксплуатации это может, поэтому в production-средах часто включают логи, но перенаправляют их на системы быстрой обработки или ротации.

**sendfile on;** 
Активация sendfile позволяет ядру копировать данные из файла в сокет напрямую, минуя пространство пользователя. Это уменьшает накладные расходы на системные вызовы (read + write) и повышает пропускную способность.

**tcp_nopush on;**
При включённом tcp_nopush (вместе с sendfile on) Nginx пытается отправлять заголовки и данные более крупными блоками, улучшая эффективность TCP-пакетизации и снижая overhead.

**tcp_nodelay on;**
Отключает ожидание накопления данных в буфере (Nagle\`s Algorithm). Полезно при отправке небольших ответов или данных в реальном времени, повышает отклик.

**gzip on;**
Включение gzip-сжатия уменьшает размер ответа, сокращает трафик и ускоряет передачу. Однако пр этом растёт нагрузка на CPU, так что уровень сжатия должен быть сбалансированным.

**gzip_min_length 10240; gzip_comp_level 1;**
 - gzip_min_length 10240 - Nginx не будет сжимать ответы меньше 10КБ, чтобы избежать неоправданных затрат CPU на мелкие ответы.
 - gzip_comp_level 1 - уровень сжатия (от 1 до 9). Уровень 1 - самый быстрый с минимальной нагрузкой на CPU.
 - gzip_vary on; gzip_disable msie6; gzip_proxied ...;

**gzip_vary on** добавляет заголовок Vary: Accept-Encoding, что помогает кэш-мерверам.
**gzip_disable msie6** отключает gzip для старых браузеров IE6 (известны проблемы).
**gzip_proxied expired no-cache no-store private auth;** указывает, в каких случаях gzip-трафик проксируется/сжимается. Все эти настройки повышают совместимость и экономят трафик.

**gzip_types** ...;
Определяет список типов контента, который стоит сжимать. Расширяет сжатие помимо text/html.

**reset_timedout_connection on;**
Если клиент завис и не отвечает, Nginx сбрасывает соединение. Это позволяет не держать "висящие" соединения, освобождая ресурсы.

**client_body_timeout 10;**
Уменьшает время ожидания получения тела запроса. Если клиент медленно отправляет данные или прерывается, это не будет долго держать соединение. Ускоряет освобождение ресурсов.

**send_timeout 2;**
Если клиент перестал читать ответ, Nginx через 2 секунды разорвет соединение. Это полезно при "зависших" клиентах и большой нагрузке.

**keepalive_timeout 30;**
keepalive_timeout 30 - соединение с клиентом сохраняется 30 секунд между запросами.

**keepalive_requests 100000** - заодно соединение клиент может сделать до 100000 запросов. использование keep-alive снижает расходы на установку нового TCP-соединения, но лимит и время нужно выбирать с учётом нагрузки.


## Директивы:
### Директива server_name \_;

> [!NOTE] Описание
> `server_name _` - используется для обозначения любого имени хоста, которое не совпадает ни с одним из явно указанных имён серверов

#### Пример:

![[Screenshot 2025-06-03 144859.png]]
В этом примере, первый блок сервера обрабатывает запросы к домену `example.com`. 
Второй блок сервера с `server_name _` будет обрабатывать любые другие запросы, не соответствующие первому
### Директива types_hash_max_size

> [!NOTE] Описание
> `types_hash_max_size` - определяет максимальный размер хеш-таблицы, используемой для хранения соответствий между типами файлов и их MIME-типами.
> - Значение по умолчанию обычно является 2048, что может быть достаточным для большинства веб-сайтов.
> - Для больших сайтов с большим разнообразием типов файлов может потребоваться увеличение этого значения.
> - Не стоит увеличивать значение без необходимости, так как это может привести к увеличению потребления памяти.
>
> `types_hash_max_size` - важная директива, влияющая на производительность Nginx при обслуживании статических файлов.

#### Пример:
![[Screenshot 2025-06-10 114118.png]]

## Выпуск сертификата LetsEnCrypt c помощью certbot

`sudo apt install certbot python3-certbot-nginx

`sudo certbot certonly -d kvlpro.ru -d www.kvlpro.ru

![[Снимок экрана от 2025-06-05 11-46-14.png]]

Добавляем сертификаты в конфигурационный файл сайта:

![[Снимок экрана от 2025-06-05 12-02-10.png]]

Создать снипет конфигурационный файл отвечающий за общие настройки ssl

`sudo vim /etc/nginx/snippets/ssl-params.conf
![[ssl_params.conf]]

Настройка задачи по обновлению сертификата:

`sudo crontab -e

+++ @daily certbot renew
## Настройка из под пользователя:
***Работа с разрешениями на папки для пользователей (например для nginx):***

Создадим непривилегированного пользователя, в нашем случае webuser:
```
adduser webuser
```
Появится интерактивный диалог, в ходе которого необходимо будет задать пароль (New password), подтвердить его (Retype new password), остальные пункты можно не заполнять, просто нажимая ENTER. В последнем вопросе Is the information correct? [Y/n] необходимо нажать Y и нажать ENTER.

Добавляем пользователя webuser в группу sudo для повышения привилегий:
```
usermod -aG sudo webuser
```


Задаем владельца каталогов и находящихся внутри файлов:
```
chown -R webuser:webuser /home/webuser/www/
```

```
chown -R webuser:webuser /home/webuser/tmp/
```

Добавляем пользователя www-data в группу webuser:
```
usermod -aG webuser www-data
```

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

пример с моего сервера:
```
location ~* \.(jpg|jpeg|gif|ico)$ {
                access_log off;
                # Кеширум на 1 минуту (s - секунда, h - час, d - день, M - месяц)
                expires 1m;
                # Более старый способ записи add_header Cache-Control public;
                add_header Pragma public;
                # Кешируем везде (и на прокси и на клиентах). 
                # private - кэширование только на браузере пользователя
                # no-cache - нельзя кэшировать
                add_header Cache-Control public;
                # Для разных клиентов (браузеров), заправшивающих один и тот же ресурс, сервер может отдавать разные версии ответа, сжатые разными способами.
                add_header Vary Accept-Encoding;
        }
```

#### 4. Настройка php-fpm

Установка php-fpm [Статья](https://losst.pro/ustanovka-nginx-i-php-fpm-v-ubuntu-20-04)

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

## Сетевое тестирование
**Программа:**
https://github.com/wg/wrk

**Установка:**
git clone https://github.com/wg/wrk.git
cd wrk
make
sudo cp wrk /usr/local/bin
cd..
sudo rm -r wrk/
		или использовать пакетный менеджер snap
sudo snap install wrk2

**Запуск:**
wrk -t12 -c400 -d30s --latency http://127.0.0.1:80
*запустить 12 потоков, установить 400 сединений и в течении 30 секунд кидать get запросы на localhost*

wrk2 -t12 -c400 -d30s --latency -R1000 https://kvlpro.ru
использовать 12 (кол-во цпу) потоков и 400 соединений (количество одновременных запросов) в течении 30 секунд с частотой 1000 запросов в секунду

**Примеры тестирования:**
***Пример 1 (worker_processes 1; worker_connections 768;)***
wrk -t4 -c2000 -d30s --latency https://kvlpro.ru
![[Снимок экрана от 2025-06-09 16-55-07.png]]
Средняя Latency: 99.63ms (неплохой результат 19.09ms)
Среднее количество запросов в секунду: 1.42k
Latency Distribution (распределение по времени задержки):
	50% было быстрее 90.68ms
	99% можно брать как худший показатель
Было выполнено 162180 запросов за 30 секунд
Socket errors (ошибки):
	1503 ошибки коннекта
	18974 ошибок чтения
	timeout 2 ошибки таймаута
5389 запросов в секунду

***Пример 2 (worker_processes 1; worker_connections 768;)***
wrk -t4 -c768 -d30s --latency https://kvlpro.ru
![[Снимок экрана от 2025-06-09 17-07-00.png]]





## Инфа с курсов:

==***Получение заголовков ответа с сервера:***==

![[Снимок экрана от 2025-06-10 12-03-14.png]]
**Статус ответа:** 200
**Content-type:** text/css

***Если у нас идет перенаправление запроса (например c без www на www) то мы увидим следующее:***
![[Снимок экрана от 2025-06-10 12-06-49.png]]
**Статус ответа:** 301 (статус ответа перенаправление)
**Content-type определяется не корректно:** text/html

Поэтому при вызове команды curl надо вызывать напрямую сайт www

==***Возврат текста через return вместо скачивания файла:***==

По умолчанию при вызове return с кодом 200 и информационным сообщением, вместо вывода на экран сообщение происходит скачивание файла, внутри которого находится информационное сообщение. Такое поведение обусловлено тем, что в файле nginx.conf в директиве http по умолчанию назначен default_type application/octet-stream; 
![[Снимок экрана от 2025-06-10 13-09-37.png]]
![[Снимок экрана от 2025-06-10 13-09-47.png]]
Чтобы переназначить поведение по умолчанию можно переопределить default_type либо глобально в директиве http файла nginx.conf либо в настройках виртуального хоста в секции server сайта
![[Снимок экрана от 2025-06-10 13-21-34.png]]
![[Снимок экрана от 2025-06-10 13-21-45.png]]

***==Поведение location:==***

![[Снимок экрана от 2025-06-10 13-29-45.png]]
![[Screenshot 2025-06-10 133005.png]]
![[Screenshot 2025-06-10 133023.png]]

***==Можно отключать логи для статических файлов, таких как картинки:==***

Т.к. файлов может быть огромное количество, то будет напрягаться процессор и диск, и увеличиваться размер файла лога:
![[Снимок экрана от 2025-06-10 17-23-50.png]]

а также изменять уровень логирования:
**error_log** - [stderr|debug|info|notice|warn|error|crit|alert|emerg]
**access_log** - имеет другие параметры
[error_log](https://nginx.org/en/docs/ngx_core_module.html?&_ga=2.205744346.856361071.1749565721-1137248912.1749565721#error_log)
[access_log](https://nginx.org/en/docs/http/ngx_http_log_module.html?&_ga=2.268790840.856361071.1749565721-1137248912.1749565721#access_log)


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

