Установить postgresql

```
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
```

Для начала необходимо добавить официальный репозиторий Zabbix. Скачиваем пакет в формате `.deb` при помощи утилиты `wget`:

```
# Для 6 версии 24.04
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-6+ubuntu24.04_all.deb
# Для 7 версии Ubuntu 24.04
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb
```

Устанавливаем скачанный файл с помощью утилиты `dpkg`:

```
dpkg -i zabbix-release_6.0-6+ubuntu22.06_all.deb
```

После того как пакет успешно был установлен, переходим к установке Zabbix-сервера и его зависимостей. Сначала обновляем списки репозиториев. Помимо самого сервера Zabbix, будет установлен интерпретатор языка PHP, агент Zabbix, необходимые плагины, а также веб-сервер Nginx:

apt update && apt -y install zabbix-server-pgsql zabbix-frontend-php php8.3-pgsql zabbix-nginx-conf zabbix-sql-scripts zabbix-agent

Создаем пользователя в PostgreSQL с именем `zabbix`

```
sudo -u postgres createuser --pwprompt zabbix
```
	Необходимо задать пароль для создаваемой учетной записи и повторить его.


Далее создаем базу данных с именем `zabbix`:

```
sudo -u postgres createdb -O zabbix zabbix
```

Импортируем файлы, содержащие схему данных, в созданную ранее БД с именем `zabbix`:

zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix

Открываем на редактирование файл `zabbix_server.conf` при помощи любого текстового редактора:
Ищем параметр `DBPassword` и задаем пароль от БД:

```
sudo vim /etc/zabbix/zabbix_server.conf
```

Далее открываем на редактирование файл `nginx.conf`:

> [!NOTE] Title
> В блоке `server` раскомментируем параметры `listen` и `server_name`, задав порт (по умолчанию используется порт 8080), на котором будет доступен веб-интерфейс Zabbix (параметр `listen`), и адрес сервера, по которому будет открываться веб-интерфейс (параметр `server_name`). Использовать можно как доменное имя, так и IP-адрес:
> 

```
sudo vim /etc/zabbix/nginx.conf
```

![[Снимок экрана от 2025-05-21 15-26-51.png]]

Перезапускаем сервер Zabbix, агент Zabbix, Nginx, PHP-FPM и добавляем их в автозагрузку:

```
sudo systemctl restart zabbix-server zabbix-agent nginx php8.3-fpm && sudo systemctl enable zabbix-server zabbix-agent nginx php8.3-fpm
```

Проверяем статус сервера Zabbix:

```
sudo systemctl status zabbix-server
```

## Настройка сервера Zabbix

http://zabbix.test.local:8080/setup.php

На первом шаге выбираем необходимый язык:
![[Снимок экрана от 2025-05-21 15-32-28.png]]
Далее система проверит наличие необходимых компонентов, их версии и необходимые технические требования:
![[Снимок экрана от 2025-05-21 15-32-20.png]]
После того как все проверки будут пройдены, необходимо проверить и при необходимости изменить параметры, связанные с базой данных. Так как ранее база данных с именем `zabbix` уже была создана, то необходимо только ввести имя пользователя БД — `zabbix` (параметр **User**) и его пароль (параметр **Password**):
![[Снимок экрана от 2025-05-21 15-55-39.png]]

В последнем шаге необходимо задать имя для Zabbix-сервера (**Zabbix server name**), указать необходимый часовой пояс (**Default time zone**) и при желании изменить тему оформления по умолчанию (**Default theme**):
![[Снимок экрана от 2025-05-21 15-57-48.png]]

Система отобразит ранее указанные настройки. Если все правильно, нажимаем на кнопку **Next step**:
![[Снимок экрана от 2025-05-21 15-58-25.png]]
После подтверждения Zabbix оповестит, что настройка успешно завершена:
![[Снимок экрана от 2025-05-21 15-59-11.png]]
После настройки отобразится форма с авторизацией в веб-интерфейсе Zabbix. По умолчанию используется логин **Admin** и пароль **zabbix**:
![[Снимок экрана от 2025-05-21 16-00-13.png]]

