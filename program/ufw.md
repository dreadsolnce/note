cОписание:

> UFW (Uncomplicated Firewall) - является самым простым и довольно  популярным инструментарием командной строки для настройки и управления брандмауэром в дистрибутивах Ubuntu и Debian. Правильно функционирующий брандмауэр является наиболее важной частью полной безопасности системы Linux. UFW позволяет сделать базовые настройки, для более сложных настроек используйте [iptables](https://1cloud.ru/help/security/ispolzovanie-iptables-na-linux).

---
###### Установка:
```
sudo apt-get install ufw
```

Состояние ufw:
```
sudo ufw status verbose
```

###### Начальная настройка:

> [!NOTE] Информация
> По умолчанию UFW настройки запрещают все входящие соединения и разрешают все исходящие. Это значит, что если кто-то попытается  достичь ваш сервер, он не сможет подключиться, в то время как любое приложение на сервере имеет доступ к внешним соединениям.  
 
Cоответствующие правила файрвола прописываются так:

```
sudo ufw default deny incoming

sudo ufw default allow outgoing
```

> [!warning] Внимание
> До включения файрвола необходимо разрешить 22 port (ssh), в противном случае мы можем потерять удалённое управление сервером

```
sudo ufw allow ssh
```
*либо*
```
sudo ufw allow 22
```

Запуск:

```
sudo ufw enable
```

###### Добавление правил:

***Разрешить HTTP (80 порт):***

```
sudo ufw allow http
```
*либо*
```
sudo ufw allow 80
```

***Разрешить HTTP (21 порт)***

```
sudo ufw allow ftp
```
*либо*
```
sudo ufw allow 21/tcp
```

> [!info]
> можно указывать в формате <номер порта>/<название протокола>

```
sudo ufw allow 3000:3100
```
```
sudo ufw allow 3000:3100/tcp
sudo ufw allow 3000:3100/udp
```

```
# Работа 
sudo ufw allow from 123.45.67.89
sudo ufw allow from 123.45.67.89 to any port 22
```

```
# Работа с диапазонами IP-адресов:
sudo ufw allow from 123.45.67.89/24
sudo ufw allow from 123.45.67.89/24 to any port 22
```

```
sudo ufw deny http
```
```
sudo ufw deny from 123.45.67.89
```

```
# Просмотр номеров правил
sudo ufw status numbered
```

```
# Удаление правила по его номеру
sudo ufw delete 2
```

*Вывести все текущие правила*

```
sudo ufw status verbose
```

*Все профили создаются вручную. Посмотреть созданные профили можно следующим образом:*

```
sudo ufw app list
```

*Разрешить доступ к программе (профилю):*

```
sudo ufw allow ZabbixAgent2
```

*Разрешить доступ к программе (профилю) для конкретного ip:*

```
sudo ufw allow from 172.16.126.9 to any app 'ZabbixAgent2'
```

***Готовый пример:***

```
ufw allow from 172.16.114.104/32 to any app 'OpenSSH'
ufw allow from 172.16.114.104/32 to any app 'ZabbixAgent2'
ufw allow out from any to 0.0.0.0/0 port 123 proto udp
ufw allow out from any to 172.16.118.3/32 app 'DNS'
ufw allow out from any to 172.16.118.4/32 app 'DNS'
```

[Ссылка №1 на статью](https://selectel.ru/blog/tutorials/how-to-configure-firewall-with-ufw-on-ubuntu-20/)

[Открытие портов по названию программ](https://1cloud.ru/help/security/ispolzovanie-utility-ufw-na-inux)