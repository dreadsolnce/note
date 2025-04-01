### Настройка индивидуальных параметров для хоста

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
ssh 