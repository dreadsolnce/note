##### Копирование ключа на удалённый хост:

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

