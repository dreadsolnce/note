# Установка и настройка

ZABBIX AGENT2 новее. Рекомендуем к использованию!

***Из стандартного репозитория операционной системы:***

deb ориентированные:
```
sudo apt install zabbix-agent2
```

***Из официального репозитория программы zabbix:***
Для rpm ориентированных:

1. Перейти на сайт https://repo.zabbix.com/zabbix/6.2/rhel/6/x86_64/
2. Выбрать нужный пакет (скопировать ссылку)
3. Установить:
```
sudo yum install https://repo.zabbix.com/zabbix/6.2/rhel/9/x86_64/zabbix-agent2-6.2.9-release1.el9.x86_64.rpm
```

для deb ориентированных:
Добавляем официальный репозиторий Zabbix. Скачиваем пакет в формате `.deb` при помощи утилиты `wget`:
```
sudo apt-get install wget
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest+ubuntu24.04_all.deb
```

Устанавливаем скачанный файл с помощью утилиты `dpkg`:
```
sudo dpkg -i zabbix-release_latest+ubuntu24.04_all.deb
```

Обновляем списки репозиториев и устанавливаем Zabbix-агент:
```
sudo apt update && sudo apt -y install zabbix-agent
```

***Настройка конфигурационного файла:***
Открываем на редактирование файл `zabbix_agentd.conf` при помощи любого текстового редактора:
```
sudo vim /etc/zabbix/zabbix_agentd.conf
или
sudo vim /etc/zabbix/zabbix_agent2.conf
```
Необходимо изменить следующие параметры:

- `Server` — необходимо задать доменное имя или IP-адрес, на котором находится сервер Zabbix. `Server` используется для пассивных проверок, когда сервер запрашивает данные у агента
![[Снимок экрана от 2025-05-21 16-25-04.png]]
- `ServerActive` — продублировать доменное имя или IP-адрес, на котором находится сервер Zabbix. Данный параметр отвечает за активный режим, когда Zabbix самостоятельно запрашивает необходимые данные. `ServerActive` - для активных проверок, когда агент сам отправляет данные на сервер
![[Снимок экрана от 2025-05-21 16-25-23.png]]
- `Hostname` — указать hostname агента в точности как он указан в системе. Чтобы узнать имя хоста можно воспользоваться командой `hostname`. Если неправильно указать имя хоста, тогда агент не сможет подключиться к серверу Zabbix.
![[Снимок экрана от 2025-05-21 16-25-39.png]]

После того как все необходимые изменения были внесены, необходимо перезапустить Zabbix-агент и добавить его в автозагрузку:
```
sudo systemctl restart zabbix-agent && sudo systemctl enable zabbix-agent
или
sudo systemctl restart zabbix-agent2 && sudo systemctl enable zabbix-agent2
```

Проверяем статус агента:
```
sudo systemctl status zabbix-agent
или
sudo systemctl status zabbix-agent2
```

## Просмотр версии

```
	/usr/sbin/zabbix_agent2 -V
```

## Просмотр конфигурационного файла

```
cat /etc/zabbix/zabbix_agent2.conf | grep -vP "^\s*$" | grep -v '#'
```

## Конфигурационный файл

LogFile=/var/log/zabbix/zabbix_agent2.log
LogFileSize=0
Server=10.?.?.?                                         # <-- zabbix server
HostnameItem=system.hostname
Include=/etc/zabbix/zabbix_agent2.d/*.conf
ControlSocket=/tmp/agent.sock
TLSAccept=psk
TLSPSKIdentity=srv-psk                                  # <-- вводится при настройке агента на сервере
TLSPSKFile=/etc/zabbix/security/psk/zabbix_agentd.psk   # <-- содержимое ключа вводится при настройке агента на сервере
Include=./zabbix_agent2.d/plugins.d/*.conf

![[Screenshot 2025-04-17 125830.png]]
# Настройка шифрования передачи <br>данных между сервером и агентом

## Настройка на стороне агента:

- **Выполняем генерацию PSK-ключа**
	- *Создаем каталог для хранения ключей:*
			- sudo mkdir -p /etc/zabbix/security/psk/
	- *Создаём ключ psk:*
			- openssl rand -hex 32 | sudo tee /etc/zabbix/security/psk/zabbix_agentd.psk
	- *Проверяем что ключ создался:*
			- sudo cat /etc/zabbix/security/psk/zabbix_agentd.psk
	- *Копируем содержимое ключа в буфер обмена либо во временный файл:*
			- содержимое необходимо будет вставить на сервере zabbix
- **Настройка конф. файла (zabbix_agent2.conf) для подключения к серверу**
	- TLSAccept=psk
	- TLSPSKIdentity=srv-psk                                  # <-- вводится при настройке агента на сервере
	- TLSPSKFile=/etc/zabbix/security/psk/zabbix_agentd.psk   # <-- содержимое ключа вводится при настройке агента на сервере
- **Доп.настройка для (zabbix_agent.conf) для подключения к серверу**
	- TLSConnect=psk

## Настройка на стороне сервера:

**Hosts (левая панель) ->** 
![[Screenshot 2025-04-17 143759.png]]
**Name (из списка) ->** 
![[Screenshot 2025-04-17 143806.png]]

**Configurations ->** 
![[Screenshot 2025-04-17 143818.png]]
**Encryption ->** 
![[Screenshot 2025-04-17 143826.png]]
**Connections to host: PSK -> <br> Connections from host: PSK -> Change PSK -> PSK identity + (Параметр TLSPSKIdentity из файла zabbix_agent2.conf) -> <br> PSK + (содержимое ключа zabbix_agentd.psk) ->** 
![[Screenshot 2025-04-17 143832.png]]
**Update**




