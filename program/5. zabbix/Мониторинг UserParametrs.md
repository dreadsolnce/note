***Мониторинг через userparametrs с помощью заранее созданного файла скрипта bash***

1. В каталоге /var/lib/zabbix/bin создать исполняемый скрипт:

```
sudo -u zabbix vim /var/lib/zabbix/bin/get_api_cookie.sh
```

![[Снимок экрана от 2026-02-17 13-39-36.png]]

```
sudo chown zabbix:zabbix /var/lib/zabbix/bin/get_api_cookie.sh
```

```
sudo chmod +x /var/lib/zabbix/bin/get_api_cookie.sh
```

2. На клиенте создать файл содержащий UserParametr в директории /etc/zabbix/zabbix_agent2.d/plugins.d

```
sudo vim /etc/zabbix/zabbix_agent2.d/plugins.d/mistral.conf
```

```
+++ UserParameter=mistral.state[*],/var/lib/zabbix/bin/get_api_cookie.sh
```

3. Перезапустить zabbix-agent

```
sudo systemctl restart zabbix-agent2.service && sudo systemctl status zabbix-agent2.service
```

4. ***На zabbix сервере создать новый шаблон (либо item в существующем шаблоне)***

`Configuration -> Templates -> Create templates`

![[Снимок экрана от 2026-02-17 15-25-10.png]]

5. Выбрать созданный шаблон и создать новый item

![[Снимок экрана от 2026-02-17 15-27-42.png]]

![[Снимок экрана от 2026-02-17 15-27-54.png]]


![[Снимок экрана от 2026-02-17 15-28-16.png]]

![[Снимок экрана от 2026-02-17 16-06-16.png]]

![[Снимок экрана от 2026-02-17 18-28-29.png]]
6. ***На zabbix сервере созданному шаблону назначить тригер ***

![[Снимок экрана от 2026-02-17 18-30-38.png]]

![[Снимок экрана от 2026-02-17 18-31-21.png]]
![[Снимок экрана от 2026-02-19 10-57-41.png]]
7. 7