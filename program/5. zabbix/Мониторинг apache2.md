***Назад:*** [[program/5. zabbix/!-----Содержание-----!|!-----Содержание-----!]]

1. **Проверить наличие модуля mod_status сервиса apache2 на локальной машине:**

```
sudo apache2ctl -M 2>/dev/null | grep status_module
```

![[Снимок экрана от 2026-01-29 11-49-37.png]]

***В случае его отсутствия установить (активировать):***

```
sudo a2enmod status
```

```
`sudo systemctl restart apache2`
```

2. **Отредактировать файл конфигурации apache2 - `/etc/apache2/mods-enabled/status.conf`:**

```
sudo vim /etc/apache2/mods-enabled/status.conf
```

- ```
    <Location "/server-status">
        SetHandler server-status
        Require ip 127.0.0.1
    </Location>
    ```

![[Снимок экрана от 2026-01-29 11-55-47.png]]

3. **Проверить на каком порту работает apache2:**

```
sudo ss -tlpn | grep apache2 | awk '{print $4}'
```

![[Снимок экрана от 2026-01-29 12-01-34.png]]

4. **Проверить, что сервер отвечает по location - server-status**

```
curl -Lk http://127.0.0.1/server-status?auto
```

![[Снимок экрана от 2026-01-29 12-02-23.png]]

5. **На сервере zabbix добавить шаблон `Apache by Zabbix` agent к нашему хосту**

![[Снимок экрана от 2026-01-29 12-07-23.png]]

6. **Проверить переменные (макросы) шаблона и при необходимости изменить их на уровне хоста:**

> [!NOTE]
> {\$APACHE.STATUS.PORT}: 80
> {\$APACHE.STATUS.SCHEME}: http
> {\$APACHE.STATUS.PATH}: server-status?auto
> {$APACHE.PROCESS_NAME}: apache2
> 

![[Снимок экрана от 2026-01-29 12-09-01.png]]

***Изменяем имя процесса:***

![[Снимок экрана от 2026-01-29 12-36-20.png]]


7. **Проверить, что метрики поступают:**

![[Снимок экрана от 2026-01-29 12-14-34.png]]

***Назад:*** [[program/5. zabbix/!-----Содержание-----!|!-----Содержание-----!]]