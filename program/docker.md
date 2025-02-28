# dockerfile

***Создание образа (images):***

docker -t <name>:tag <путь к docker файлу>
```
docker build -t test_site:1 .
```
***Запуск контейнера из образа:***

 docker run -d -it --rm --name <имя контейнера> -p 8080:80 <имя образа>
	-d  : запуск в фоновом режиме
	-it : -i заходим в интерактивном режиме, -t использовать tty
	-rm : удаление контейнера после его остановки
	-p  : проброс портов
```
docker run --name test_site -d -it test_site:1 
```

***Подключиться к контейнеру:***

docker exec -it <имя контейнера> <команда>

```
docker exec -it test_site bash
```
***Подключиться к контейнеру запустить команду и сохранить вывод на локальном компьютере:***

```
docker run --rm test_site:2 cat /usr/local/apache2/conf/httpd.conf > my-httpd.conf
```

***Удалить контейнер:***

docker rm <имя контейнера>

```
docker rm test_site
```
##### Примеры:
№1 dockerfile php-apache
```
FROM php:8.3-apache

COPY ./site /var/www/html
```


# docker compose

***Создание (сборка) образа***

```
docker compose build
```

***Запуск dockercompose в фоновом режиме***

```
docker compose up -d
```
***Остановить и удалить контейнеры***

```
docker compose down
```

***Выводит журналы сервисов***

```
docker-compose logs -f [service name]
```
где [service name] - имя сервиса в dockercompose.yml

![[Screenshot 2025-02-14 130539.png]]

[Ссылка на ресурс:](https://habr.com/ru/articles/881882/)
