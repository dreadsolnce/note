***Запуск определённой команды через sudo без пароля:***

```
sudo visudo -f /etc/sudoers.d/runcmd
```

```
kvl    ALL=NOPASSWD:/usr/bin/ls
```

![[Снимок экрана от 2026-04-16 11-54-09.png]]

```
sudo chmod 440 nopass
```
