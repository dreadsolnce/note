#### Ошибка - закрытие удалённой сессии через некоторое время неиспользования:
[ссылка на ресурс](https://dampi.ru/oshibka-client_loop-send-disconnect-broken-pipe)
На сервере создать файл /etc/ssh/sshd_config.d/alive.conf в каталоге пользовательских конфигураций:
```
sudo vim /etc/ssh/sshd_config.d/alive.conf
```
++++++++++++++++++++++++++
```
ClientAliveInterval=200
ClientAliveCountMax=3
```

Перезагрузить службу:
```
sudo systemctl restart ssh.service
```

[[os/ssh/!------Содержание------!|!------Содержание------!]]
