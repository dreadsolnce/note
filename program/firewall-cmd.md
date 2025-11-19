fire## Открываем порт для внешних соединений всем пользователям


> [!important] ВНИМАНИЕ
> **Перед тем как добавлять правило, выключите из автозагрузки firewall-cmd и запланируйте перезагрузку сервера если , что то пойдет не так сервер перезагрузится и firewall будет выключен**
> 	- `sudo systemctl disable firewalld.service`
> 	- `sudo shutdown -r +15` (через 15 минут)
> **Отменить запланированную перезагрузку:**
> 	- `sudo shutdown -c`
> **Просмотр запланированной перезагрузки:**
> 	- `sudo cat /var/log/messages | grep reboot`

firewall-cmd --state
sudo firewall-cmd --list-all
firewall-cmd --permanent --add-port=21/tcp
firewall-cmd --remove-port=21/tcp #эту команду используйте, чтобы закрыть порт

Важные шаги

- **Добавление порта:**
    
    используйте `--permanent` для того, чтобы правило сохранялось после перезагрузки системы.
    
- **Применение изменений:**
    
    после добавления всех нужных портов выполните команду `firewall-cmd --reload` для активации правил.

Либо применить настройки после их внесения:
```
sudo firewall-cmd --runtime-to-permanent
```

[Ссылка](https://www.dmosk.ru/miniinstruktions.php?mini=firewalld-centos)

https://winitpro.ru/index.php/2019/10/18/nastrojka-firewalld-linux-centos/

Просмотр всех доступных служб:
```
firewall-cmd --get-services
```

Разрешить службу ssh
```
sudo firewall-cmd --zone=public --add-service=ssh --permanent
```

Удалить запретить остальным доступ по ssh
`firewall-cmd --permanent --remove-service=ssh`

Применям правила
`firewall-cmd --reload`

Правило для конкретного ip
`firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.10" service name="ssh" accept'`

Удалить правило:
```
firewall-cmd --remove-rich-rule='rule family="ipv4" source address="192.168.1.100" reject'
```

Удалить правило:
```
sudo firewall-cmd --permanent --remove-rich-rule='rule family="ipv4" source address="10.8.0.1/24" port port="22" protocol="tcp" accept'
```

Правило для службы zabbix-agent:
```
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="62.109.9.232" service name="zabbix-agent" accept'
```

Добавить правило для конкретного порта и ip адреса:
```
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="188.255.123.193" port port="993" protocol="tcp" accept'
```

Добавить правило для OpenVPN:
```
sudo firewall-cmd --permanent --zone=<OpenVPN-zone> --add-rich-rule='rule family="ipv4" source address="<OpenVPN-network/CIDR>" port port="22" protocol="tcp" accept'
```

Применям правила
```
sudo firewall-cmd --reload
```