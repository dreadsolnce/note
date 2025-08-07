[статья](https://jino.ru/spravka/articles/f2b.html#ustanovka)

```
sudo apt install fail2ban
sudo touch /etc/fail2ban/jail.local
sudo systemctl enable fail2ban
```

### Разблокировать ip адрес

1. ***Смотрим кто заблокирован и по каким правилам:***

`fail2ban-client status`

*пример результата вывода этой команды:*

Status
|- Number of jail:      2
`- Jail list:   nginx-limit-req, sshd

*видно, что в клетке 2 адреса и правило блокировки «nginx-limit-req».*

2. ***Смотрим какие это IP адреса:***

`fail2ban-client status sshd

3. ***Разблокируем один, например: 188.255.123.193:***

```
fail2ban-client set sshd unbanip 188.255.123.193
```
### Специальная настройка для протокола ssh

```
sudo vim /etc/fail2ban/jail.local
```

![[Screenshot 2025-06-02 165634.png]]


> [!NOTE] Описание
> Этот набор условий означает, что IP-адрес, с которого было произведено 6 неудачных попыток 
> SSH-подключения за последний час (3600 секунд), будет заблокирован на одни сутки (86400 секунд). 
> Не будут блокироваться IP-адреса в диапазоне от 127.0.0.1 до 127.0.0.8 и адрес 23.34.45.56.

```
[sshd]
enabled   = true
maxretry  = 2
findtime  = 1h
bantime   = 7d
ignoreip  = 127.0.0.1/8 185.173.3.33
```

```
sudo systemctl restart fail2ban
```

**Просмотр активных jail-ов:**

sudo fail2ban-client status

**Просмотр статуса конкретного jail-а**

```
sudo fail2ban-client status sshd
```

### Специальная настройка для nginx от Bruteforce (подбор паролей авторизации)

```
sudo vim /etc/fail2ban/jail.local
```

![[Screenshot 2025-06-20 104038.png]]

*Также можно добавить еще одну строку вида:*

`action = iptables-multiport[name=nginx-http-auth, port="http,https", protocol=tcp]
`
![[Снимок экрана от 2025-06-20 10-49-30.png]]

> [!NOTE] Описание
> Этот набор условий означает, что IP-адрес, с которого было произведено 3 неудачных попытки 
> авторизации за последний 6 минут (600 секунд), будет заблокирован на один час (3600 секунд). 

```
[nginxHttpAuth]
enabled = true
port = http,https
filter = nginxHttpAuth
logpath = /var/log/nginx/error.log
maxretry = 3
findtime = 600
bantime = 3600
backend = auto
action = iptables-multiport[name=nginxHttpAuth, port="http,https", protocol=tcp]
```

```
sudo systemctl restart fail2ban
```

**Просмотр активных jail-ов:**

sudo fail2ban-client status

**Просмотр статуса конкретного jail-а**

```
sudo fail2ban-client status nginx-http-auth
```


**Просмотр логов fail2ban**
```
sudo tail -f /var/log/fail2ban.log
```

![[Снимок экрана от 2025-06-20 10-48-12.png]]

### Специальная настройка от DoS-атак

Суть атаки сводится к тому, чтобы «забить» канал связи бессмысленным трафиком и исчерпать ресурсы сервера. 
При этом не легитимные запросы должны отработать до такой степени, чтобы было невозможно обработать легитимные.

**Настройка (подготовка) nginx сервера:**

> [!NOTE] Описание
> **Подготовка nginx-limit-req.** Попробуем защитить веб-сервер с помощью модуля nginx-limit-req. 
> Он нужен для определения максимального всплеска запросов (burst) и пороговых значений разделяемой памяти (zone). 
> В случае, если эти параметры будут превышены, модуль добавит в лог-файл новые записи. С помощью них Fail2ban определяет и блокирует вредоносные IP-адреса.

Модуль nginx-limit-req входит в nginx-core, и дополнительная его установка не потребуется. В директиве http добавим параметры для включения модуля и определения зоны:

```
sudo vim /etc/nginx/nginx.conf
```

```
	limit_req_zone $binary_remote_addr zone=one:10m rate=5r/s;
```

![[Снимок экрана от 2025-06-20 12-37-06.png]]

> [!tip] Title
> Устанавливает зону общей памяти и максимальный размер пакета запросов. Если скорость запросов превышает скорость, настроенную для зоны (**5 запросов в секунду**), 
> их обработка задерживается таким образом, чтобы запросы обрабатывались с определенной скоростью. Избыточные запросы задерживаются до тех пор, 
> пока их количество не превысит максимальный размер пакета, в этом случае запрос завершается с [ошибкой](https://nginx-org.translate.goog/en/docs/http/ngx_http_limit_req_module.html?_x_tr_sl=en&_x_tr_tl=ru&_x_tr_hl=ru&_x_tr_sch=http#limit_req_status).

Далее в директиве location достаточно добавить значение для ограничения количества запросов:

```
limit_req zone=one burst=5;
```

![[Снимок экрана от 2025-06-20 12-40-16.png]]

разрешать в среднем не более 5 запросов в секунду и 5 соединений. Другими словами на одно соединение 1 запрос в секунду.
Теперь при превышении количества соединений, который мы задали в burst, в /var/log/nginx/error.log будут сыпаться ошибки.
Таким образом мы фиксируем DDos атаку с удалённого компьютера, например через wrk и с помощью fail2ban сможем отправить в ban компьютер.

```
sudo vim /etc/fail2ban/jail.local
```

![[Снимок экрана от 2025-06-20 17-20-07.png]]

*Также можно добавить еще одну строку вида:*

`action = iptables-multiport[name=nginx-http-auth, port="http,https", protocol=tcp]`

![[Снимок экрана от 2025-06-20 18-04-19.png]]

> [!NOTE] Описание
> Этот набор условий означает, что IP-адрес, с которого было произведено 5 превышений 
> параметра nginx-limit-req которые фиксируются в журнале /var/log/nginx/error.log 
> (будем думать что он нас атакует) за последний 10 секунд, будет заблокирован на 30 секунд. 

```
[nginx-limit-req]
port     = http,https
enabled  = true
logpath  = /var/log/nginx/error.log
maxretry = 5
findtime = 10
bantime  = 30
backend  = auto
action = iptables-multiport[name=nginx-limit-req, port="http,https", protocol=tcp]

```

```
sudo systemctl restart fail2ban
```

**Просмотр активных jail-ов:**

sudo fail2ban-client status

**Просмотр статуса конкретного jail-а**

```
sudo fail2ban-client status nginx-http-auth
```


**Просмотр логов fail2ban**
```
sudo tail -f /var/log/fail2ban.log
```