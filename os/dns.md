xqLkpOgPO89ua7!1[Ссылка на ресурс:](https://interface31.ru/tech_it/2022/10/nastraivaem-otkazoustoychivyy-dns-server-na-baze-bind-9.html)
## Проверка работы dns:

##### Проверка работы обратной зоны
```
nslookup 192.168.192.101
```

![[Снимок экрана от 2025-03-27 16-46-10 3.png]]

##### Проверка работы прямой зоны
```
nslookup ai-app1p.test.local
```

![[Снимок экрана от 2025-03-27 17-16-32.png]]

##### Проверка dns запросов (проверка работы удалённого сервера dns)

```
`dig @<IP_адрес_сервера> <доменное_имя>`
```

![[Снимок экрана от 2025-08-21 16-42-12.png]]

![[Снимок экрана от 2025-08-21 16-41-59.png]]

## Настройка systemd-resolved для локального кэширования

[Ссылка на ресурс](https://zevilz.dev/posts/496/)

***Путь настроек кэширующего резолвера:***

```
cat /etc/systemd/resolved.conf
```
##### Проверка текущих настроек резолвера

```
resolvectl status
```

![[Снимок экрана от 2025-03-27 17-26-00.png]]

## Настройки netplan:

***Путь сохранения настроек для netplan:***

```
ls /etc/netplan/00-installer-config.yaml
```

***Изменение конфигурации ip dns сервера:***

```
sudo systemctl status systemd-resolved.service
```

```
cd /etc/netplan
```

```
sudo cp 00-installer-config.yaml 00-installer-config.yaml.original
```

```
dig @172.16.126.15 dev.border-patrol.ru
```

```
sudo vim 00-installer-config.yaml
```

*Проверить корректность конфигурационного файла Netplan для синтаксической проверки*
```
sudo netplan generate
```

*Для предварительного применения конфигурации и проверки её работоспособности в течение 120 секунд, после чего конфигурация будет отменена или подтверждена*

```
sudo netplan try
```

*или сразу применить новую конфигурацию:*

```
sudo netplan apply
```

*Очистить кэш dns записей:*

```
sudo systemd-resolve --flush-caches
sudo systemctl restart systemd-resolved.service
```

*Проверка применения новых dns серверов:*

```
sudo systemd-resolve --status
```

```
resolvctl status
```

```
nslookup 172.16.3.204
```

## Настройки CentOS:

*Конфигурационный файл:*

```
sudo cat /etc/sysconfig/network-scripts/ifcfg-ens192
```

```
sudo systemctl status systemd-resolved.service
```

*Текущий сервер dns:*

```
nslookup 172.16.3.204
```

```
dig dev.border-patrol.ru
```

```
ip addr show
```

```
nmcli connection show ens192
```

```
sudo systemctl restart NetworkManager.service
```

