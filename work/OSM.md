### Установка для UBUNTU 20.04

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
osm2pgsql --create -U osm -W --slim -H localhost -d gis --hstore --multi-geometry --number-processes 10 --tag-transform-script /home/osm/openstreetmap-carto/openstreetmap-carto.lua  --style /home/osm/openstreetmap-carto/openstreetmap-carto.style -C 10000 /home/osm/data/azerbaijan-200101.osm.pbf
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

# +++
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

