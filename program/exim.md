Описание: почтовый сервер для отправки почты

***Команда для отправки почты:***

```
echo "«Привет из exim»!" | mail -s Test kvl@kvlpro.ru
```

***Файл конфигурации /etc/exim4/update-exim4.conf.conf:***

```console
dc_eximconfig_configtype='smarthost'
dc_other_hostnames=''
dc_local_interfaces='127.0.0.1'
dc_readhost='exim.ru-central1.internal'
dc_relay_domains=''
dc_minimaldns='true'
dc_relay_nets=''
dc_smarthost='mail.kvlpro.ru::587'
CFILEMODE='644'
dc_use_split_config='false'
dc_hide_mailname='true'
dc_mailname_in_oh='true'
dc_localdelivery='mail_spool'
```

***Файл с паролями клиентов от почтовых ящиков /etc/exim4/passwd.client:***

```console
# password file used when the local exim is authenticating to a remote
# host as a client.
#
# see exim4_passwd_client(5) for more documentation
#
# Example:
### target.mail.server.example:login:password
mail.kvlpro.ru:kvl@kvlpro.ru:<пароль в открытом виде>
```

***Создать зашифрованный пароль пользователя:***
```
sudo /usr/share/doc/exim4-base/examples/exim-adduser
```

Скопировать получившийся пароль из файла passwd и вставить его вместо открытого пароля в файле passwd.client

***/etc/exim4/passwd.client:***

```console
# password file used when the local exim is authenticating to a remote
# host as a client.
#
# see exim4_passwd_client(5) for more documentation
#
# Example:
### target.mail.server.example:login:password
mail.kvlpro.ru:kvl@kvlpro.ru:$1$DIqu$k0zQ78GvRzdSc0wYiTkcl1
```



