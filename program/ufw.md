Описание:

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
sudo ufw allow from 123.45.67.89
```
```
sudo ufw allow from 123.45.67.89 to any port 22
```
```
sudo ufw deny http
```
```
sudo ufw deny from 123.45.67.89
```
```
sudo ufw status numbered
```
```
sudo ufw delete 2
```


[Ссылка №1 на статью](https://selectel.ru/blog/tutorials/how-to-configure-firewall-with-ufw-on-ubuntu-20/)
