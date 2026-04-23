***Назад:*** [[program/5. zabbix/!-----Содержание-----!|!-----Содержание-----!]]

#### ***zabbix-agent***

> [!NOTE] Информация
> Файлы (такие как pgsql.bgwriter.sql, pgsql.scans.sql, pgsql.wal.stat.sql и другие) используются официальным шаблоном Zabbix:
> PostgreSQL by Zabbix agent (в старых версиях он мог называться Template DB PostgreSQL).



#### ***zabbix-agent-2***

2. **Проверить наличие плагина zabbix-agent2-plugin-postgresql**

***Проверяем наличие конфигурационного файла плагина:***

```
ls /etc/zabbix/zabbix_agent2.d/plugins.d/postgresql.conf
```

![[Снимок экрана от 2026-01-29 12-58-10.png]]

***В случае отсутствия файла устанавливаем плагин:***

- Узнаем версию установленного агента:

```
/usr/sbin/zabbix_agent2 -V | head -n 1
```

![[Снимок экрана от 2026-01-29 13-02-07.png]]

В зависимости от версии установленного агента скачиваем плагин:
[Ссылка для скачивания плагина zabbix-agent2-plugin-postgresql](https://cdn.zabbix.com/zabbix-agent2-plugins/sources/)

- Разархивируем скачанный архив перейдя в директорию куда скачали файл:

```
ls zabbix-agent2-plugin-postgresql* | xargs tar -xf >/dev/null && ls -d zabbix-agent2-plugin-postgresql*/ | tail -n 1
```

```
cd zabbix-agent2-plugin-postgresql-7.0.5/
```

- Для установки агента необходимы установленные доп. пакеты:


> [!NOTE] Title
> make 
> golang-go 
> build-essential

- Проверяем наличие пакетов в системе и при необходимости устанавливаем не достающие:

```
apt-cache policy make golang-go build-essential
```


> [!tip] Замечание
> [Установка на centOS ver.9](https://timeweb.cloud/tutorials/go/ustanovka-go-na-centos)

- Выполнить команду по сборке программы:
```
make
```

- Перенести скомпилированный файл в директорию в /etc/zabbix/scripts/

```
sudo mkdir -p /etc/zabbix/scripts/ && sudo cp zabbix-agent2-plugin-postgresql /etc/zabbix/scripts
```

- Изменить владельца:

```
sudo chown zabbix:root /etc/zabbix/scripts/zabbix-agent2-plugin-postgresql
```

- Удалить каталог:
```
cd .. 
rm -r <path to directory>
```

2. **Настройка плагина:**

- Создать конфигурационный файл со следующим содержимым:

```
sudo -u zabbix vim /etc/zabbix/zabbix_agent2.d/plugins.d/postgresql.conf
```
- 
```
	Plugins.PostgreSQL.System.Path=/etc/zabbix/scripts/zabbix-agent2-plugin-postgresql
	
```

- Перезапустить агента zabbix

```
sudo systemctl restart zabbix-agent2.service
```

3. **Настройка базы данных postgresql:**

- Подключаемся к базе данных:

Генерируем пароль для пользователя zabbix базы данных postgres

```
openssl rand -base64 12
```

Подключаемся к базе данных

```
sudo -u postgres -i 
```

```
psql
```


- Создаём отдельного пользователя, через которого Zabbix будет подключаться к серверу PostgreSQL

```
CREATE USER zbx_monitor WITH PASSWORD 'password' INHERIT; 
```

```
GRANT pg_monitor TO zbx_monitor;
```

```
\q
```

```
exit
```

4. **Настройка postgresql:**

- Отредактируем конфигурационный файл pg_hba.conf, чтобы разрешить нашему клиенту подключаться к серверу PostgreSQL.

```
sudo vim /etc/postgresql/13/main/pg_hba.conf
```
либо
```
sudo vim /var/lib/pgsql/data/pg_hba.conf
```
либо
```
sudo -u postgres vim /data/pgpro/std-17/data/pg_hba.conf
```

- 
```
	host all zbx_monitor 127.0.0.1/32 md5
```

![[Снимок экрана от 2026-01-29 14-48-16.png]]

- Перечитаем конфигурационные файлы сервера PostgreSQL:
```
sudo systemctl reload postgresql
```

5. **Настройка на zabbix сервере:**

На zabbix сервере добавить встроенный шаблон PostgreSQL Agent2:

![[Снимок экрана от 2026-01-29 14-50-13.png]]

- Не нажимая кнопку Update отредактировать шаблон (макросы) нажав на шаблон и перейдя во вкладку Macros:
___
![[Снимок экрана от 2026-01-29 14-57-05.png]]
___
![[Снимок экрана от 2026-01-29 15-05-45.png]]

> [!NOTE] Title
> ```
> {$PG.PASSWORD} : <password>
> {$PG.USER} :  zbx_monitor
> ```
> 

6. **Проверка работы шаблона:**

![[Снимок экрана от 2026-01-29 15-07-50.png]]

7. 7
8. 