# Установка

1. Удалить ранее установленные (конфликтующие) пакеты:
		`for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done`
2. Настроить репозиторий программы docker
```
		# Add Docker's official GPG key:
		sudo apt-get update
		sudo apt-get install ca-certificates curl
		sudo install -m 0755 -d /etc/apt/keyrings
		sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
		sudo chmod a+r /etc/apt/keyrings/docker.asc
		# Add the repository to Apt sources:
		echo \
		"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
		$(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
		sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
		sudo apt-get update
```
3. Установить программу docker
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
4. Настройка после установки
```
sudo groupadd docker
```

```
sudo usermod -aG docker $USER
```

```
newgrp docker
```

```
docker run hello-world
```

# команды управления

***Просмотр занятого места на диске:***
```
docker system df
```

***Создание образа (images):***

```
docker -t <name>:tag <путь к docker файлу>

docker build -t test_site:1 .
```

***Запуск контейнера из образа:***
```
 docker run -d -it --rm --name <имя контейнера> -p 8080:80 <имя образа>
	-d  : запуск в фоновом режиме
	-it : -i заходим в интерактивном режиме, -t использовать tty
	-rm : удаление контейнера после его остановки
	-p  : проброс портов
```
docker run --name test_site -d -it test_site:1 

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

***Получить информацию об объектах (image, volum, network...):***

docker image inspect <имя объекта>

```
docker image inspect 9a9a9fd723f1
docker volume inspect data
и т.д.
```

***Создать коммит контейнера (сделать свой контейнер) не рекомендуется:***

docker commit <id контейнера> <новое имя контейнера>

```
docker exec -it nginx bash
--> `сделать изменения`
exit

docker commit 05d0a393198e nginx:myimage
```
# dockerfile

***Создание своего образа:***

1. Создаём docker файл:

```
vim Dockerfile
```

2. Добавляем инструкции:

FROM nginx

RUN touch /123.txt

COPY index.html /usr/share/nginx/html/index.html

3. CСобираем (создаём) свой образ:

```
docker build -t nginx:mynginx .
```

4. Пушим наш образ в docker hub для дальнейшего его использования:


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
