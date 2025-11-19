__Оглавление__

1. [[#Установка на Astra Linux 1.8 Воронеж]]
2. [[#Установка на Ubuntu 20.04]]
3. [[#Обновление карт OSM]]
	- [[#Вариант номер 1 (требует очистки кэша)]]
	- [[#Вариант номер 2 (с удалением грязных страниц)]]


# Установка на Astra Linux 1.8 Воронеж

__Подключенные репозитории__

*cat /etc/apt/sources.list*

```
deb https://download.astralinux.ru/astra/stable/1.8_x86-64/repository-extended/ 1.8_x86-64 main contrib non-free non-free-firmware
deb https://download.astralinux.ru/astra/stable/1.8_x86-64/repository-main/ 1.8_x86-64 main contrib non-free non-free-firmware
deb https://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
```


*cat /etc/apt/sources.list.d/postgres-iso.list*

```
deb cdrom:[postgres]/astra-smolensk/1.8/ 1.8_x86-64 main
```

***Установка базы данных***

```
sudo apt install postgrespro-std-17-server
sudo apt install libgdal32 libboost-serialization1.74.0 libgmpxx4ldbl libsfcgal1
sudo apt install postgis-std-17
sudo dpkg -i /media/postgis-std-17_3.3.7-2.18x8664_amd64.deb
```


# Установка на Ubuntu 20.04
##### 1. ***Установка системных пакетов:***
```
sudo apt install libboost-all-dev git tar unzip wget bzip2 build-essential autoconf libtool libxml2-dev libgeos-dev libgeos++-dev libpq-dev libbz2-dev libproj-dev munin-node munin protobuf-c-compiler libfreetype6-dev libtiff5-dev libicu-dev libgdal-dev libcairo2-dev libcairomm-1.0-dev apache2 apache2-dev libagg-dev liblua5.2-dev ttf-unifont lua5.1 liblua5.1-0-dev
```
##### 2. ***Установка базы данных***
```
sudo apt-get install postgresql postgresql-contrib postgis postgresql-12-postgis-3 postgresql-12-postgis-3-scripts
```
##### 3. ***Настройка базы данных*** 
```
sudo -u postgres -i
createuser osm

# Создать базу данных принадлежащую конкретному пользователю
createdb -E UTF8 -O osm gis

# Переходим в консоль программы PostgreSQL
psql

# Устанавливаем подключение к базе данных
\c gis

# Устанавливаем расширение PostGIS на базу PostgreSQL
CREATE EXTENSION postgis;
CREATE EXTENSION hstore;

# Назначаем хозяина _rendered таблице geometry_columns:
ALTER TABLE geometry_columns OWNER TO osm;
ALTER TABLE spatial_ref_sys OWNER TO osm;

# Выходим из консоли программы PostgreSQL
\q
exit
```
##### 4. ***Создаем системного пользователя osm**
```
# Создаем пользователя osm, чтобы сервер тайлов мог работать от имени пользователя `osm`. 
# Следующая команда создаст системного пользователя без пароля.

sudo adduser --system --group --home /home/osm/ osm
```

##### 5. *Загрузка таблицы стилей карты и данные карты* *****

###### a) ***Предоставьте разрешения текущей учетной записи на каталог osm.***
```
sudo apt install acl

cd /home/osm

# Предоставьте разрешения текущей учетной записи на каталог osm.
sudo setfacl -R -m u:<test_user>:rwx /home/osm/
```
###### б) ***Загрузка таблицы стилей карт CartoCSS в домашний каталог пользователя `osm` с помощью git (а) или tar архивом (б)***
```
# Загрузка таблицы стилей карт CartoCSS в домашний каталог пользователя `osm` с помощью git (а) или tar архивом (б)
	а) git clone https://github.com/gravitystorm/openstreetmap-carto.git
	
	б) wget https://github.com/gravitystorm/openstreetmap-carto/archive/v5.7.0.tar.gz
		tar xvf v5.7.0.tar.gz
		mv openstreetmap-carto-5.7.0/ openstreetmap-carto
		rm -r v5.7.0.tar.gz
```
###### в) ***Загрузка карты***
```
# Загрузка карты
mkdir /home/osm/data
cd /home/osm/data
wget -c https://download.geofabrik.de/asia/azerbaijan-latest.osm.pbf
```
##### 6. *Оптимизация производительности сервера PostgreSQL* *****

[Документация по настройке](https://ru.linux-terminal.com/?p=3318)
##### 7. *** Импорт карты в PostgreSQL***
```
sudo apt install osm2pgsql
```

###### a) *Предоставление разрешения пользователю postgres.*
```
sudo setfacl -R -m u:postgres:rwx /home/osm/

sudo setfacl -R -m u:postgres:rwx /home/osm/
```

###### б) *Импорт данных карты в базу данных*
```
cd /home/osm/openstreetmap-carto
sudo -u postgres -i
```
  1) Для выполнения команды osm2pgsql от имени пользователя базы данных osm необходимо либо настроить файл pg_hba.conf:
`sudo vim /etc/postgresql/12/main/pg_hba.conf`
--- host    all             all             127.0.0.1/32            **md5**	
+++ host    all             all             127.0.0.1/32            trust
- Перезагрузить службу:
`sudo systemctl restart postgresql@12-main.service`

2) Либо задать пароль пользователя osm базы данных:
```
sudo -u postgres -i
psql
ALTER USER osm WITH PASSWORD '<password>';
\q
# и в команду osm2pgsql добавить ключ -W
```

```
osm2pgsql --create -U osm -W --slim -H localhost -d gis --hstore --multi-geometry --number-processes 10 --tag-transform-script /home/osm/openstreetmap-carto/openstreetmap-carto.lua  --style /home/osm/openstreetmap-carto/openstreetmap-carto.style -C 10000 /home/osm/data/azerbaijan.osm.pbf
```

Команда выполненная на BorderOSM:
```
osm2pgsql --create --slim --number-processes 16 -C 32000 -U osm -W -H localhost -d gis --hstore --multi-geometry -O flex -S openstreetmap-carto-flex.lua  /home/osm/data/azerbaijan-latest.osm.pbf
```

> `-d`            : База данных, с которой мы работаем
> `--create`       : Загрузить данные в пустую базу вместо того, чтобы пытаться дополнить уже имеющиеся.
> `--append`       : Обновить (Добавить) существующую карту.
> `--slim`         : osm2pgsql может использовать различные схемы таблиц, “slim” подходит для рендеринга.
> `--hstore`       : Позволяет использовать для рендеринга тэги, для которых нет отдельных колонок в базе данных.
> `--multi-geometry`: создание объектов с несколькими геометриями в таблицах postgresql.
> `--tag-transform-script` : Указывает на lua-скрипт, используемый при обработке тэгов.
> `-C 10000`             : Выделяет 10 Гб оперативной памяти для osm2pgsql на процесс импорта
> `--number-processes 10`  : Использует 10 процесса. Если у вас есть больше процессорных ядер, вы можете указать больше.
> `--style`              : Создаёт колонки в базе данных, как указано в этом файле
> -W                   : запрос пароля пользователя базы данных

![[Screenshot 2025-02-25 155634.png]]

***в) Предоставляем все привилегии базы данных `gis` пользователю `osm`.***
```
psql -c "GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO osm;" -d gis
exit
```

##### 8. ***Установка Renderd и mod_tile***
- `renderd` — демон для рендеринга тайлов OpenStreetMap из базы данных PostgreSQL
- `mod_tile` — это модуль Apache, который используется для предоставления плиток клиентам (например, веб-браузерам).

a) Добавляем репозиорий:
```
ls /etc/apt/apt.conf.d/99verify-peer.conf
echo Acquire { https::Verify-Peer false } | sudo tee /etc/apt/apt.conf.d/99verify-peer.conf

sudo apt install ca-certificates
sudo add-apt-repository ppa:osmadmins/ppa

Добавить параметр [trusted=yes] после deb в файле /etc/apt/sources.list.d/osmadmins-ubuntu-ppa-focal.list
  +++ deb [trusted=yes] https://ppa.launchpad.net/osmadmins/ppa/ubuntu focal main

sudo apt update
```

б) Устанавливаем пакеты:

```
sudo apt install apache2 libapache2-mod-tile renderd
```

`#Файл конфигурации для `renderd` будет создан по адресу `/etc/apache2/conf-available/renderd.conf`

в) Включаем модуль tile:
```
sudo a2enmod tile
```

г) Создаём виртуальный хост для тайлового сервера.
```
sudo vim /etc/apache2/sites-available/tileserver_site.conf
```

```
<VirtualHost *:80>
    ServerName tile.your-domain.com   
    DocumentRoot /var/www/html/osm_app
    LogLevel info
 
    <Directory /var/cache/renderd/tiles>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride None
        Require all granted

        ModTileTileDir /var/cache/renderd/tiles
        LoadTileConfigFile /etc/renderd.conf
        ModTileEnableStats On
        ModTileBulkMode Off
        ModTileRequestTimeout 3
        ModTileMissingRequestTimeout 10
        ModTileMaxLoadOld 2
        ModTileMaxLoadMissing 5
        ModTileRenderdSocketName /run/renderd/renderd.sock
        ModTileCacheDurationMax 604800
        ModTileCacheDurationDirty 900
        ModTileCacheDurationMinimum 10800
        ModTileCacheDurationMediumZoom 13 86400
        ModTileCacheDurationLowZoom 9 518400
        ModTileCacheLastModifiedFactor 0.20
        ModTileEnableTileThrottling Off
        ModTileEnableTileThrottlingXForward 0
        ModTileThrottlingTiles 10000 1
        ModTileThrottlingRenders 128 0.2
    </Directory>

</VirtualHost>
```

`#Замените `tile.your-domain.com``

д) Включаем виртуальный хост
```
sudo a2ensite tileserver_site.conf
sudo systemctl restart apache2
sudo systemctl status apache2
systemctl status renderd
```

##### 9. Создание таблицы стилей Mapnik

1) Установка необходимых пакетов.
```
sudo apt-get update && sudo apt install curl unzip gdal-bin mapnik-utils libmapnik-dev python3-pip nodejs npm libmapnik3.0 python3-cairo python3-mapnik
```
Проверка установки mapnik:
```
python3
import mapnik
quit()
```

2) Установка пакета `carto` с помощью `npm`.
```
sudo npm install -g carto
```

3) Установка модуля Python `yaml` и `psycopg2`.
```
sudo apt install python3-pretty-yaml python3-yaml python3-psycopg2
```

4) Выполняем настройку:
```
sudo -u postgres -i

cd /home/osm/openstreetmap-carto/

./scripts/get-external-data.py
./scripts/get-fonts.sh
```

5) Cоздаём таблицу стилей Mapnik XML с помощью компилятора таблицы стилей карты `carto`
```
carto --quiet project.mml > style.xml
```
1) Создаем индексы
```
psql -d gis -f indexes.sql
psql -c "GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO osm;" -d gis
exit
```

7) Установка дополнительных шрифтов
```
sudo apt install fonts-dejavu 
sudo apt install fonts-noto-cjk fonts-noto-cjk-extra fonts-noto-hinted fonts-noto-unhinted
```

##### 10. Настройка рендеринга

- Настройка файла sudo vim /etc/renderd.conf:

В разделе `[renderd]` изменить количество потоков в соответствии с количеством ядер ЦП.
```
	num_threads=10
```

Добавьте слой `default`. 

```
	; ADD YOUR LAYERS:
	[default]
	URI=/osm/
	XML=/home/osm/openstreetmap-carto/style.xml
	HOST=tile.your-domain.com
```

По умолчанию renderd допускает максимальный уровень масштабирования 18. 
Если нужен уровень масштабирования 19, добавьте следующую строку в раздел `[default]`.

```
MAXZOOM=19
```

![[Screenshot 2025-02-25 170152.png]]

- Настраиваем службу renderd
```
sudo mkdir /etc/systemd/system/renderd.service.d/

sudo vim /etc/systemd/system/renderd.service.d/custom.conf

	[Service]
	User=osm
	RuntimeDirectory=renderd 
```


- Изменяем владельца:
```
sudo chown osm /run/renderd/ -R
sudo chown osm /var/cache/renderd/tiles/ -R
```

- Перезапуск служб:
```
sudo systemctl daemon-reload

sudo systemctl restart renderd
```

- Проверка службы на наличие ошибок:

```
sudo journalctl -eu renderd
```

##### 11. Проверка подключения (apache)

```
curl http://192.168.192.31/osm/0/0/0.png --output start.png
ls start.png
rm start.png
```

##### 12. Настройка подключения (apache) для клиента

```
sudo a2dissite 000-default.conf
sudo mkdir /var/www/html/osm_app
cd /var/www/html/
sudo mv index.html index.html.apache
sudo wget https://github.com/Leaflet/Leaflet/releases/download/v1.9.4/leaflet.zip
sudo unzip leaflet.zip
#sudo mv dist/* osm_app
sudo rm -r leaflet.zip dist

sudo vim /var/www/html/osm_app/index.html

<html>
<head>
<meta charset="UTF-8">
<title>My first osm</title>
<link rel="stylesheet" type="text/css" href="leaflet.css"/>
<script type="text/javascript" src="leaflet.js"></script>
<style>
   #map{width:100%;height:100%}
</style>
</head>

<body>
  <div id="map"></div>
  <script>
    var map = L.map('map').setView([55,0.8],6);
    L.tileLayer('http://tile.your-domain.com/osm/{z}/{x}/{y}.png',{maxZoom:18}).addTo(map);
</script>
</body>
</html>

```

```
systemctl reload apache2
```

##### 12. Предварительная визуализация тайлов


> [!NOTE] Важно
> Предварительно отрисованные тайлы будут кэшироваться в каталоге `/var/cache/renderd/tiles/`.
> Смотри файл /etc/renderd.conf параметр: tile_dir=/var/cache/renderd/tiles в секции [renderd]

```sql
render_list -m default -a -z 0 -Z 19 --num-threads=10
```
> - m : см. файл /etc/renderd.conf


> [!NOTE] Замечание
> После обновления данных карт, необходимо повторно выполнить предварительную визуализацию всех фрагментов, используя параметр `--force`.
Данный параметр затирает тайлы.


```
render_list -m default -a -z 0 -Z 19 --num-threads=10 --force
```

##### 13. Включение https

#### a) Для зарегистрированого домена:

Установить пакет snapd, т.к. классический пакет python3-certbot-apache имеет ошибки
```
sudo apt install snapd
sudo snap install --classic certbot
```

Получаем и устанавливаем сертификат:
```
sudo /snap/bin/certbot --apache --agree-tos --redirect --hsts --staple-ocsp --must-staple --email <email> -d <tile.your-domain.com>
```

> --register-unsafely-without-email : вы не сможете получать уведомления о приближающемся истечении срока действия или отзыве ваших сертификатов или проблемах с установкой Certbot, которые приведут к невозможности продления (для теста пойдет).

Отредактировать файл index.html и заменить весь протокол HTTP на HTTPS:
```
sudo sed -i 's/http/https/g' /var/www/html/osm_app/index.html
```
#### б) Для локального домена:

***Создать сертификат используя один из двух вариантов:***
- 1-й вариант:
```
cd ~
openssl req -x509 -out osm-test.crt -keyout osm-test.key   -newkey rsa:2048 -nodes -sha256   -subj '/CN=localhost' -extensions EXT -config <( \
   printf "[dn]\nCN=localhost\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:localhost\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```
- 2-й вариант:
```
cd ~
openssl req -new -x509 -days 30 -keyout server.key -out server.pem
```
> на вопрос «Enter PEM pass phrase:» отвечаем паролем
> на вопрос «Common Name (eg, YOUR name) []:» отвечаем именем сайта, для которого создаем сертификат, например osm,test.local

- Снимаем пароль с ключа:
```
cp server.key{,.orig}
openssl rsa -in server.key.orig -out server.key
rm server.key.orig
```

***Далее перемещаем сертификат и ключ в нужную нам директорию:***

```
sudo -u osm mkdir /home/osm/certs
sudo mv osm-test.crt  osm-test.key /home/osm/certs
```

***Включить модуль ssl для apache:***
```
sudo a2enmod ssl

systemctl restart apache2

systemctl status apache2
```

 ***Добавить виртуальный хост VirtualHost *:443 (защищённое соединение) 
 в файл /etc/apache2/sites-available/tileserver_site.conf***

sudo vim /etc/apache2/sites-available/tileserver_site.conf
```bash
<VirtualHost *:80>
    ServerName osm.test.local   
    DocumentRoot /var/www/html/osm_app
    LogLevel info
 
    <Directory /var/cache/renderd/tiles>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride None
        Require all granted

        ModTileTileDir /var/cache/renderd/tiles
        LoadTileConfigFile /etc/renderd.conf
        ModTileEnableStats On
        ModTileBulkMode Off
        ModTileRequestTimeout 3
        ModTileMissingRequestTimeout 10
        ModTileMaxLoadOld 2
        ModTileMaxLoadMissing 5
        ModTileRenderdSocketName /run/renderd/renderd.sock
        ModTileCacheDurationMax 604800
        ModTileCacheDurationDirty 900
        ModTileCacheDurationMinimum 10800
        ModTileCacheDurationMediumZoom 13 86400
        ModTileCacheDurationLowZoom 9 518400
        ModTileCacheLastModifiedFactor 0.20
        ModTileEnableTileThrottling Off
        ModTileEnableTileThrottlingXForward 0
        ModTileThrottlingTiles 10000 1
        ModTileThrottlingRenders 128 0.2
    </Directory>

</VirtualHost>





<VirtualHost *:443>
     SSLEngine on
     SSLCertificateFile /home/osm/certs/osm-test.crt
     SSLCertificateKeyFile /home/osm/certs/osm-test.key
     
#     SSLCertificateChainFile /etc/ssl/chain.crt

    ServerName osm.test.local
    DocumentRoot /var/www/html/osm_app
    LogLevel info

    <Directory /var/cache/renderd/tiles>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride None
        Require all granted

        ModTileTileDir /var/cache/renderd/tiles
        LoadTileConfigFile /etc/renderd.conf
        ModTileEnableStats On
        ModTileBulkMode Off
        ModTileRequestTimeout 3
        ModTileMissingRequestTimeout 10
        ModTileMaxLoadOld 2
        ModTileMaxLoadMissing 5
        ModTileRenderdSocketName /run/renderd/renderd.sock
        ModTileCacheDurationMax 604800
        ModTileCacheDurationDirty 900
        ModTileCacheDurationMinimum 10800
        ModTileCacheDurationMediumZoom 13 86400
        ModTileCacheDurationLowZoom 9 518400
        ModTileCacheLastModifiedFactor 0.20
        ModTileEnableTileThrottling Off
        ModTileEnableTileThrottlingXForward 0
        ModTileThrottlingTiles 10000 1
        ModTileThrottlingRenders 128 0.2
    </Directory>

</VirtualHost>
#+++

```

***Назначить подключение по https по умолчанию выполнив команду:***
```
sudo sed -i 's/http/https/g' /var/www/html/osm_app/index.html
```

***Перезагрузить службу:***
```
sudo systemctl restart apache2.service 
```

Открыть в браузере страницу: https://osm.test.local/

***Сделать редирект с http:// на https://***
- sudo a2enmod alias
- sudo service apache2 restart
- Добавить в файл /etc/apache2/sites-enabled/tileserver_site.conf строку: `Redirect / https://osm.test.local/`

![[Снимок экрана от 2025-03-03 16-00-10 1.png]]

***Перезагрузить службу:***
```
sudo systemctl restart apache2.service 
```

***Проверить, введя в браузере*** `http://osm.test.local/`, ***что открывается страница*** `https://osm.test.local/`

##### 14. Ограничение доступа к серверу

```bash
sudo vim /etc/apache2/sites-enabled/tileserver.conf
```

***Добавьте следующие строки в теги .***

```sql
    <Location /osm>
        SetEnvIf Referer test\.local trusted_referer
        Order deny,allow
        Deny from all
        Allow from env=trusted_referer
    </Location>
```
> Приведенный выше код проверяет, включает ли заголовок HTTP-реферера ваш собственный домен (test.local). В противном случае доступ к каталогу `/osm` будет запрещен. Обратная косая черта используется для экранирования символа точки. Иными словами доступ имеют только те, кто входит в домен test.local

![[Снимок экрана от 2025-03-03 17-47-04.png]]

***Чтобы добавить несколько имен хостов в качестве доверенных источников перехода, используйте следующий синтаксис.***
```sql
SetEnvIf Referer (example\.com|www\.example\.com|map\.example\.com) trusted_referer
```

***Сохраните и закройте файл. Затем проверьте синтаксис.***
```bash
sudo apache2ctl -t
```

***Если синтаксис в порядке, перезагрузите Apache, чтобы изменения вступили в силу.***
```bash
sudo systemctl reload apache2
```


> [!important] Замечание
> Т.к. при установке программы renderd у нас создаётся конфигурационный файл **/etc/apache2/conf-available/renderd.conf**, то если данный файл включён **/etc/apache2/conf-enabled/renderd.conf**, то настройки программы renderd в файле виртуального хоста **/etc/apache2/sites-available/tileserver_site.conf** можно не прописывать, т.к. apache подгружает конф. файлы из каталога conf-enabled автоматом. Таким образом, если мы отключим конфигурационный файл renderd.conf командой `a2disconf renderd`, то рендеринг (отрисовка) перестанет работать. Что бы рендеринг заработал мы можем либо раскоментировать параметры в файле **sites-available/tileserver_site.conf** либо снова запустить конф. файл **conf-available/renderd.conf** командой `a2enconf renderd`
> ![[Снимок экрана от 2025-03-03 16-38-46.png]]
> 

[Ссылка на инструкцию](http://use.openstreetmap.ru/serving-tiles/manually-building-a-tile-server-20-04-lts/)

### Обновление карт

- ***Подключить по протоколу ssh на сервер***
- ***Выполнить команду screen -  утилиту экрана, чтобы сохранить сеанс***
```
screen

-> Enter
```
> **Ctrl+A**, отпустить эти клавиши, а затем нажать клавишу **D**, чтобы отключиться от текущего сеанса Screen. Вы увидите сообщение, как показано ниже.
>   `detached from 32113.pts-1.buster`
> Это говорит о том, что идентификатор предыдущего сеанса Screen — 32113. Можно выйти из сеанса SSH и даже выключить компьютер. 
> При этом процесс импорта OSM все еще выполняется. Когда нужно вернуться и проверить ход импорта, можно снова подключиться к своему 
> серверу по SSH и выполнить следующую команду, чтобы получить предыдущий идентификатор сеанса экрана.
>    `screen -ls`
> Пример вывода:
>    `There is a screen on:`
>    `32113.pts-1.buster (05/19/2020 03:45:29 PM) (Detached)`
>    `1 Socket in /run/screen/S-linux-terminal.`
> Затем можно повторно подключиться к предыдущему сеансу Screen.
>    `screen -r 32113`
> И вы сможете продолжить свою работу.

- ***Создать резервную копию базы данных***
```
mkdir /home/osm/backup && cd /home/osm/backup
pg_dump -U osm -h localhost -d gis > osm_db_backup_$(date +%F).sql

-> Ввести пароль пользователя osm базы данных
```
- ***Выполнить обновление карты***
```
sudo -u postgres -i

osm2pgsql --append -U osm -W --slim -H localhost -d gis --hstore --multi-geometry --number-processes 10 --tag-transform-script /home/osm/openstreetmap-carto/openstreetmap-carto.lua  --style /home/osm/openstreetmap-carto/openstreetmap-carto.style -C 10000 <новый_путь_к_файлу_карты_pbf>

exit
```
- ***Выполнить удаление старых тайлов***
```
sudo systemctl stop renderd

sudo rm -r /var/cache/renderd/tiles/default/*

sudo systemctl start renderd
```
- 

### Ошибки и их решение

##### warning: unable to find face-name 'Noto Emoji Bold' in FontSet 'fontset-2'

Изменить строку в файле openstreetmap-carto/scripts/get-fonts.sh

--- curl --fail -A "get-fonts.sh/osm-carto" -o "${TMPDIR}/Noto_Emoji.zip" -L 'https://fonts.google.com/download?family=Noto%20Emoji'
+++ curl --fail -A "get-fonts.sh/osm-carto" -o "${TMPDIR}/Noto_Emoji.zip" -L 'https://archive.org/download/noto-emoji/Noto_Emoji.zip'

##### warning: unable to find face-name 'HanaMinB Regular' in FontSet 'fontset-2'

Установить шрифты в систему:
```
sudo apt-get install fonts-hanazono
```

##### An error occurred while loading the map layer 'default': Postgis Plugin: ERROR:  function carto_path_type(text, text) does not exist
```
psql -d gis -f /home/osm/openstreetmap-carto/functions.sql 
```


Изменение службы (добавление дополнительного каталога для размещения файла sock)
sudo systemctl edit postgrespro-std-17.service

```
[Service]
RuntimeDirectory=postgresql
```

>`systemctl RuntimeDirectory` — это опция в конфигурационных файлах `systemd`, которая указывает, какие каталоги должны быть созданы в директории `/run` (или `/var/run`) при запуске службы.

# Обновление карт OSM
___
##### Вариант номер 1 (требует очистки кэша)

> [!question] Важное замечание
> Временной интервал на котором тестировалось обновление:
> 2025-09-01 : 2025-11-13 Обновление ***успешно прошло*** (2,5 месяца)
> 2025-01-01 : 2025-11-13 Обновление ***ошибка*** (11,5 месяцев)

---
Ручной запуск команды ***рендеринга*** без использования службы:
```
sudo -u osm renderd -f -c /etc/renderd.conf 
```
![[Снимок экрана от 2025-11-12 10-34-53.png]]

Просмотр логов службы ***renderd:***
```
sudo journalctl -f -n 50 -u renderd.service
```
![[Снимок экрана от 2025-11-12 10-43-05.png]]

Запуск рендеринга (рисовка тай лов в cache):
```
render_list -m default -a -z 0 -Z 19 --num-threads=16
```

> [!NOTE] Информация
> Параметр: --num-threads : количество потоков в соответствии с количеством ядер ЦП на вашем сервере. *Например если num-threads равно 4, то это значит, что одновременно могут быть задействованы 4 worker-а (см. рисунок)* 
> Параметр: -z 0 -Z 19 : уровень масштабирования
> 
![[Снимок экрана от 2025-11-12 11-54-28.png]]

Проверка работы от рисовки в браузере и определение какой масштаб в текущий момент:
```
sudo journalctl -f -n 20 -u apache2
```
![[Снимок экрана от 2025-11-12 12-07-46.png]]

Необязательный шаг ***удалить*** существующую базу и ***создать*** новую
- ***Удаление базы данных***
```
sudo systemctl stop renderd.service
```

```
sudo rm -rf /data/cache/renderd/tiles/default/*
```

```
sudo -u postgres -i
```

```
psql
```

```
\l
```

```
DROP DATABASE IF EXISTS gis;
```

```
\q
```
![[Снимок экрана от 2025-11-13 11-41-56.png]]
___
- ***Создание базы данных***
```
createdb -E UTF8 -O osm gis
```

```
psql
```

```
\c gis
```

```
CREATE EXTENSION postgis;
```

```
CREATE EXTENSION hstore;
```

```
ALTER TABLE geometry_columns OWNER TO osm;
```

```
ALTER TABLE spatial_ref_sys OWNER TO osm;
```

```
psql -c "GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO osm;" -d gis
```

```
\q
```

![[Снимок экрана от 2025-11-13 10-18-13.png]]
___
- ***Импорт*** скачанных готовых данных из файла osm.pbf в базу данных postgresql 
```
osm2pgsql --create --slim --number-processes 16 -C 32000 -U osm -W -H localhost -d gis --hstore --multi-geometry -O flex -S /home/osm/openstreetmap-carto/openstreetmap-carto-flex.lua /home/osm/data/azerbaijan.osm.pbf
```
![[Снимок экрана от 2025-11-13 10-26-56.png]]
```
psql -c "GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO osm;" -d gis
```
![[Снимок экрана от 2025-11-13 12-15-17.png]]
___
- ***Убедиться***, что создались таблицы: **planet_osm_ways, planet_osm_nodes*
```
psql
```
```
\c gis
```
```
\dt
```
```
\q
```
![[Снимок экрана от 2025-11-13 10-31-41.png]]
```
cd /home/osm/openstreetmap-carto
```

```
./scripts/get-external-data.py
```

```
./scripts/get-fonts.sh
```
![[Снимок экрана от 2025-11-13 11-59-51.png]]
```
psql -d gis -f /home/osm/openstreetmap-carto/functions.sql
```
![[Снимок экрана от 2025-11-13 15-11-25.png]]
```
psql -c "GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO osm;" -d gis
```

```
exit
```

```
sudo systemctl start renderd.service
```

```
sudo systemctl status renderd.service
```

```
sudo -u osm renderd -f -c /etc/renderd.conf 
```
___
- ***Установка программы Pyosmium*** для загрузки diff файлов с серверов osm
[Официальный сайт программы Pyosmium](https://docs.osmcode.org/pyosmium/latest/user_manual/10-Replication-Tools/)
```
sudo apt install python3.11-venv
```

```
sudo -u osm mkdir /home/osm/venv
```

```
sudo chmod a+trwx chronograf/data/
```

```
python3 -m venv /home/osm/venv/venvPyosmium
```
![[Снимок экрана от 2025-11-13 10-42-51.png]]
```
source /home/osm/venv/venvPyosmium/bin/activate
```

```
pip install --upgrade pip
```

```
pip install osmium
```

```
pyosmium-get-changes --version
```
![[Снимок экрана от 2025-11-13 10-44-48.png]]
![[Снимок экрана от 2025-11-13 10-46-19.png]]
___
- Узнаем с какого ***числа*** начинать обновления. Если у вас все еще есть файл **OSM**, который вы использовали для настройки базы данных (azerbaijan-251111.osm.pbf), создайте ***файл состояния*** следующим образом:
```
pyosmium-get-changes -O /home/osm/data/azerbaijan.osm.pbf -f /home/osm/data/sequence.state -v
```
```
cat /home/osm/data/sequence.state
```
![[Снимок экрана от 2025-11-13 10-55-30.png]]
> [!important] Важно
> Запомните url (***https://download.geofabrik.de/asia/azerbaijan-updates***) в выводе команды (он понадобится при получении обновлений)

___
- Теперь можно ***создать (скачать)*** файл с изменениями

> [!NOTE] Информация
> pyosmium-get-changes загружает только около ***100 МБ обновлений*** одновременно (около 8 часов обновлений планеты). Если вы хотите больше, то добавьте параметр -size
> - 1Gb - 3 дня обновлений планеты
> - 10GB - 30 дней обновлений планеты

```
pyosmium-get-changes --server https://download.geofabrik.de/asia/azerbaijan-updates -f /home/osm/data/sequence.state -o /home/osm/data/newchange.osc.gz
```

```
ls -l /home/osm/data/newchange.osc.gz
```
![[Снимок экрана от 2025-11-13 11-30-33.png]]
___
- ***Применить*** (обновить базу данных postgresql) скачанные изменения 
Использовать ту же команду, что и при создании базы данных, только добавить вместо файла osm.pbf файл с изменениями
```
osm2pgsql --append --slim --number-processes 16 -C 32000 -U osm -W -H localhost -d gis --hstore --multi-geometry -O flex -S /home/osm/openstreetmap-carto/openstreetmap-carto-flex.lua /home/osm/data/newchange.osc.gz
```
![[Снимок экрана от 2025-11-13 13-17-12.png]]

- ***Очистить cache***
```
sudo systemctl stop renderd.service
```

```
sudo rm -r /data/cache/renderd/tiles/default/*
```

```
sudo systemctl start renderd.service
```

- ***Простой скрипт*** для получения обновлений

```
while true; do
  # pyosmium-get-changes would not overwrite an existing change file
  rm -f newchange.osc.gz
  # get the next batch of changes
  pyosmium-get-changes -f sequence.state -o newchange.osc.gz
  # save the return code
  status=$?

  if [ $status -eq 0 ]; then
    # apply newchange.osc.gz here
    ....
  elif [ $status -eq 3 ]; then
    # No new data, so sleep for a bit
    sleep 60
  else
    echo "Fatal error, stopping updates."
    exit $status
  fi
done
```

##### Вариант номер 2 (с удалением грязных страниц)
[Сайт с приведенной ниже инструкцией](https://switch2osm.org/serving-tiles/updating-as-people-edit-osm2pgsql-replication/#Creating-scripts-to-apply-updates)
- **Инициализируем** репликацию
```
sudo -u osm osm2pgsql-replication init -d gis --osm-file /home/osm/data/azerbaijan.osm.pbf
```
![[Снимок экрана от 2025-11-14 10-32-15.png]]

- ***Создаем файл логов*** и скрипт для применения обновлений и удаления грязных плиток
```
sudo mkdir /var/log/tiles
```

```
sudo chown osm /var/log/tiles
```

```
sudo vim /usr/local/sbin/expire_tiles.sh
```

`Пример файла expire_tiles.sh`
```
#!/bin/bash
render_expired --map=s2o --min-zoom=13 --max-zoom=20 -s /run/renderd/renderd.sock < /data/cache/renderd/dirty_tiles.txt
rm /data/cache/renderd/dirty_tiles.txt
```
> [!NOTE] Title
> Будет предпринята попытка перерисовать все грязные тайлы, начиная с уровня масштабирования 13.

`Еще один вариант файла expire_tiles.sh` - ***более предпочтительный***
```
#!/bin/bash
render_expired --min-zoom=13 --touch-from=13 --delete-from=18 --max-zoom=19 -s /run/renderd/renderd.sock -t /data/cache/renderd/tiles < /data/cache/renderd/dirty_tiles.txt
```
> [!NOTE] Title
> Плитки до уровня 12 игнорируются, плитки с 
> уровнями масштабирования 13–19 помечаются как грязные, а плитки с уровнем масштабирования 19 удаляются

![[Снимок экрана от 2025-11-14 10-33-53.png]]

- ***Создаем основной*** выполняемый скрипт

```
sudo vim /usr/local/sbin/update_tiles.sh
```

```
#!/bin/bash
osm2pgsql-replication update -d gis --post-processing /usr/local/sbin/expire_tiles.sh --max-diff-size 100  --  --number-processes 16 -C 32000 --hstore --multi-geometry -O flex -S /home/osm/openstreetmap-carto/openstreetmap-carto-flex.lua --expire-tiles=1-19 --expire-output=/data/cache/renderd/dirty_tiles.txt
```

> [!NOTE] Title
> `--max-diff-size 100`сколько данных обрабатываем за раз, но учтите, что osm2pgsql-replication фактически будет повторять загрузку данных и обновление базы данных до тех пор, пока не останется данных для обработки, что может занять некоторое время. определяет `--max diff-size`объём данных, извлекаемых за каждую итерацию.

```
sudo setfacl -m u:osm:rwx /usr/local/sbin/expire_tiles.sh /usr/local/sbin/update_tiles.sh
```

```
sudo chmod ugo+x /usr/local/sbin/update_tiles.sh /usr/local/sbin/expire_tiles.sh
```

![[Снимок экрана от 2025-11-14 11-32-37.png]]

- Включаем ***отладочные сообщения*** (только на этапе тестирования)

> [!NOTE] Title
> Чтобы увидеть вывод процесса рендеринга тайлов, чтобы убедиться, что тайлы, помеченные как грязные, обрабатываются. По умолчанию в последних версиях mod_tile эта функция отключена.

```
sudo systemctl edit renderd.service
```

```
[Service]
Environment=G_MESSAGES_DEBUG=all
```

```
sudo systemctl daemon-reload
```

```
sudo systemctl restart renderd
```

```
sudo systemctl restart apache2
```

- ***Тестирование***

В другом сеансе:

```
sudo tail -f /var/log/syslog
```

или если вы используете Debian 12, в котором по умолчанию нет файла «syslog»:

```
sudo journalctl -ef
```

Запускаем скрипт:
```
sudo -u osm /usr/local/sbin/update_tiles.sh
```

![[Снимок экрана от 2025-11-14 11-38-46.png]]
![[Снимок экрана от 2025-11-14 11-38-55.png]]

- ***Добавляем cron***
Изменяем файл update_tiles.sh
```
#!/bin/bash
echo
echo "update_tiles.sh run:" `date`
#
if ! command -v osm2pgsql-replication &> /dev/null
then
    echo "osm2pgsql-replication could not be found"
    exit 1
fi
#
if [[ -f /var/cache/renderd/update_tiles.sh.running ]]
then
    echo "update_tiles.sh already running; /var/cache/renderd/update_tiles.sh.running exists"
    exit 1
else
    touch /data/cache/renderd/update_tiles.sh.running
fi
#
if ! osm2pgsql-replication update -d gis --post-processing /usr/local/sbin/expire_tiles.sh --max-diff-size 20  -- --number-processes 16  -C 32000 --hstore --multi-geometry -O flex -S /home/osm/openstreetmap-carto/openstreetmap-carto-flex.lua --expire-tiles=1-19 --expire-output=/data/cache/renderd/dirty_tiles.txt > /data/cache/renderd/osm2pgsql-replication.$$ 2>&1
then
    echo "osm2pgsql-replication error"
    cat /var/cache/renderd/osm2pgsql-replication.$$
else
    grep Backlog /var/cache/renderd/osm2pgsql-replication.$$ | tail -1
    rm /var/cache/renderd/osm2pgsql-replication.$$
fi
#
rm /var/cache/renderd/update_tiles.sh.running
#
```

osm2pgsql-replication update -d gis --post-processing /usr/local/sbin/expire_tiles.sh --max-diff-size 10  --  --number-processes 16 -C 32000 --hstore --multi-geometry -O flex -S /home/osm/openstreetmap-carto/openstreetmap-carto-flex.lua \
    --expire-tiles=1-19 --expire-output=/data/cache/renderd/dirty_tiles.txt