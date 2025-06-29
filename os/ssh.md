##### Копирование ключа на удалённый host:

ssh-copy-id -i /home/kvl/.ssh/id_rsa.pub administrator@ai-back1p
![[Снимок экрана от 2025-04-23 14-06-59.png]]

#### Настройка подключения по ssh:

Использовать два файла: ~/.ssh/config либо общесистемный /etc/ssh/ssh_config

```
Host example.com
	HostName example.com
	Port 22
	User myusername
	IdentityFile ~/.ssh/id_rsa_example
	ConnectTimeout 10
	Compression yes 
```

![[Снимок экрана от 2025-04-23 14-11-20.png]]

#### Ошибка - закрытие удалённой сессии через некоторое время неиспользования:
[ссылка на ресурс](https://dampi.ru/oshibka-client_loop-send-disconnect-broken-pipe)
На сервере создать файл  /etc/ssh/sshd_config.d/alive.conf в каталоге пользовательских конфигураций:
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
#### Как скопировать ssh-ключи с одной машины на другую

Хочу немного затронуть эту тему отдельно. Генерировать ключ на новой машине не обязательно. Но нужно выполнить такие действия

- Скопировать id_rsa и id_rsa.pub со старой машины на новую
- Посмотреть права на файлы, возможно, ключи окажутся слишком "открытыми" для записи и потребуется сменить им права доступа - sudo chmod 700 ~/.ssh/*
- Выполнить команду ssh-add

#### Настройка доступа к серверу только по ssh ключам:

Чтобы обезопасить себя от возможного брутфорса (взлома пароля простым перебором), 
необходимо отключить возможность такого входа.

```
sudo vim /etc/ssh/sshd_config
```

+++ PasswordAuthentication no

```
sudo service ssh restart
```