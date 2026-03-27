1. Создание пользователя для мониторинга с минимально необходимыми правами

**Подключение:**

```
sudo -u root mysql
```


```
CREATE USER 'zbx_monitor'@'localhost' IDENTIFIED BY 'password';
```

```
GRANT REPLICATION CLIENT, PROCESS, SHOW DATABASES, SHOW VIEW ON *.* TO 'zbx_monitor'@'localhost';
```

```
-- Для версий MariaDB 10.5.9+ также требуется:
GRANT SLAVE MONITOR ON *.* TO 'zbx_monitor'localhost'%';
```

```
FLUSH PRIVILEGES;
```


***Проверить подключение к базе из под пользователя zbx_monitor***

```
mysql -u zbx_monitor -p
```

![[Снимок экрана от 2026-02-11 12-11-32.png]]

2. Настройка zabbix-agent2

Плагин MySQL вшит в zabbix-agent2 по умолчанию 

***Проверка плагина***

```
sudo -u zabbix zabbix_agent2 -R metrics | grep -A 13 "Mysql"
```

![[Снимок экрана от 2026-02-11 11-13-32.png]]

***Внести изменения в конфигурационный файл плагина mysql***

```
sudo vim /etc/zabbix/zabbix_agent2.d/plugins.d/mysql.conf
```

```
+++ Plugins.Mysql.Default.Uri=tcp://localhost:3306
+++ Plugins.Mysql.Default.User=zbx_monitor
+++ Plugins.Mysql.Default.Password=password
```

```
cat /etc/zabbix/zabbix_agent2.d/plugins.d/mysql.conf | grep -v '^[#[:space:]]' | grep -vP "^\s*$"
```

![[Снимок экрана от 2026-02-11 12-14-47.png]]

```
sudo systemctl restart zabbix-agent2.service && sudo systemctl status zabbix-agent2.service
```


```
sudo -u zabbix zabbix_agent2 -R metrics | grep -A 13 "Mysql"
```


> [!NOTE] Информация
> Статус станет активным после того как мы добавим метрику на сервер


3. Настройка сервера:

![[Снимок экрана от 2026-02-11 12-21-25.png]]

![[Снимок экрана от 2026-02-11 12-23-14.png]]

```
{$MYSQL.DSN}        :   tcp://127.0.0.1:3306
{$MYSQL.PASSWORD}   :   пароль
{$MYSQL.USER}       :   zbx_monitor
```

![[Снимок экрана от 2026-02-11 12-27-36.png]]

4. 4
5. 5
6. 6