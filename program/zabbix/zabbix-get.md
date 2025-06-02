# Установка

```
sudo apt install zabbix-get
```

# Использование

```
zabbix_get -s <host-name-or-IP> -p 10050 -k agent.ping
```

```
zabbix_get -s <host-name-or-IP> -p 10050 -k agent.version
```

```
zabbix_get -s <host-name-or-IP> -p 10050 -k agent.hostname
```

```
zabbix_get -s <host-name-or-IP> -p 10050 -k system.cpu.load[all,avg1]
```

<h1>Usage:</h1>

> [!NOTE] Title
> zabbix_get -s host-name-or-IP \[-p port-number] \[-I IP-address] -k item-key
> zabbix_get -s host-name-or-IP \[-p port-number] \[-I IP-address]
 >.............--tls-connect cert --tls-ca-file CA-file
> .............\[--tls-crl-file CRL-file] \[--tls-agent-cert-issuer cert-issuer]
> .............\[--tls-agent-cert-subject cert-subject]
> .............--tls-cert-file cert-file --tls-key-file key-file
> .............\[--tls-cipher13 cipher-string] \[--tls-cipher cipher-string]
> .............-k item-key
> zabbix_get -s host-name-or-IP \[-p port-number] \[-I IP-address]
>..............--tls-connect psk --tls-psk-identity PSK-identity
>..............--tls-psk-file PSK-file \[--tls-cipher13 cipher-string]
>..............\[--tls-cipher cipher-string] -k item-key
> zabbix_get -h
> zabbix_get -V
>

<h1> Example Python Script </h>

```
import subprocess
subprocess.call(["zabbix_get", "-s", "<host-name-or-IP>", "-p", "10050", "-k", "agent.version"])
```


