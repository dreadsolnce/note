
# Установка и настройка

```
sudo apt install zabbix-agent2
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
	- 
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




