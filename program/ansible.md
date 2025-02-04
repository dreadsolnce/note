
##### Установка программы

> [!NOTE] Доп. информация
> Для работы Ansible нужно установить на управляющем и управляемых хостах ssh и python.
> 
> Дополнительно, если будет использоваться подключение к управляемым хостам через логин и пароль, а не через ssh ключи, то необходимо установить утилиту sshpass

**server**

```
sudo apt-get install ssh sshpass net-tools ansible
```

```
ansible --version
```

```
python --version
```
**client**

```
python --version
```

```
sudo apt-get install ssh net-tools
```

---
##### Настройка конфигурации программы ansible

Открываем файл конфигурации /etc/ansible/ansible.cfg
В случае отсутствия каталога или папки создаем файл вручную.

```
vim /etc/ansible/ansible.cfg
```

> [!success] Пример файла
> ``` 
> [defaults]
> host_key_checking = false
> inventory = /var/ansible/hosts.txt
> log_path = /var/log/ansible/ansible.log
> 

> **[defaults]**                                - настройки по умолчанию.
> **host_key_checking**                          - отключение проверки отпечатка
> **inventory**                          - чтобы не писать всегда ключ -i при запуске ansible.
> **log_path** - log-файл.
> **ask_pass**                          - включить/выключить запрос пароля. По умолчанию стоит false. 
> Если вы используете ключ для подключения, то менять нет необходимости.
> **ask_sudo_pass**                          - По умолчанию указано TRUE. Это запрос sudo-пароля на 
> удаленном сервере. Если на удаленном сервере отключен запрос sudo-пароля, то менять 
> нет необходимости. Если запрос пароля необходим, измените данное значение на FALSE.
>  **forks**                          - Количество параллельных процессов ansible. По умолчанию 5. Если
>   у вас много серверов, то данное значение можно изменить. Тогда ansible будет 
>   выполнять настройки параллельно не на пяти серверах, а, например, на двадцати. 
>   Однако, нагрузка на сервер и сеть вырастет.
> 

##### Основные команды:

1. ***Стандартный модуль:***

**Пинг хостов :***
```
ansible servers -i hosts.ini -m ping
```
> servers – имя группы из файла инвентаризации
> -i – расположение файла инвентаризации
> -m – указание модуля Ansible. В примере используется модуль ping, который просто проверяет доступность хоста.

```
ansible servers -i hosts.ini -m ping -k
```
> -k - позволяет ввести пароль вручную, не задавая его в файле инвентари

```
ansible all -i hosts.ini -m ping
```
> all - запустить на всех хостах из файла инвентари

***Модуль command:***

Время хостов на сервере
```
ansible servers -i hosts.ini -m command -a "date" -k
```
тоже что и с участием оболочки shell но без спецсимволов "<", ">", "|", ";", "&"

***Модуль shell***

Информация о ядре
```
ansible servers -i hosts.ini -m shell -a 'uname -a'
```

```
ansible servers -i hosts.ini -m shell -a 'grep DISTRIB_RELEASE /etc/lsb-release' 
```

***Модуль copy - копирование файла***
```
ansible ubuntu-srv1 -i hosts.ini -m copy -a 'src=/etc/motd dest=/tmp/ owner=test group=test mode=644'
``` 

> ubuntu-srv1 - запустить только на одном хосте

2. ***Модуль setup - специализируется на сборе фактов с узлов.***

```
ansible servers -i hosts.ini -m setup
```

Можно фильтровать ключи, если интересует что-то конкретное. Например, можно узнать, сколько памяти доступно на всех хостах:

```
ansible servers -i hosts.ini -m setup -a 'filter=ansible_memtotal_mb'
```

```
ansible servers -i hosts.ini -m setup -a 'filter=ansible_loadavg'
```

```
 ansible servers -i hosts.ini -m setup -a 'filter=ansible_cmdline'
```

##### Настройка файла инвентаризации

*Создадим в домашнем каталоге отдельный каталог с конфигами:*
```
mkdir ~/ansible.cfg && cd ~/ansible.cfg && touch hosts.ini
```

`vim hosts.ini`

> [!success] Пример файла
> ``` 
> [servers]
> ubuntu-srv1 ansible_host=192.168.192.11 ansible_port=22  ansible_ssh_user=kvl 
> ansible_ssh_password=12345678 ansible_sudo_pass=12345678
> ubuntu-srv2 ansible_host=192.168.192.11 ansible_port=22 ansible_user=kvl
> ansible_ssh_private_key_file=/kvl/home/.ssh/id_rsa
> 
> [ubuntu] 
> host0.example.org
> 
> [debian]  
> host[1-2].example.org
> 
> [linux:children]
> ubuntu
> debian

> **[servers]**                                - название группы серверов (любое).
> [**groupname:children**]     - дочерние группы
> **ansible_host**                          - реальный ip адрес хоста
> **ansible_port**                          - порт подключения ssh.
> **ansible_ssh_private_key_file** - путь до закрытого ключа. Для входа без участия пароля.
> **ansible_ssh_user**                          - имя пользователя для подключения на удаленный сервер.
> **ansible_ssh_pass**                          - пароль пользователя.
> **ansible_sudo_pass** - пароль для sudo для выполнения на удалённом сервере


> [!important] Важно
> лучше использовать `ansible_ssh_user=root` чтобы избежать потенциальных проблем с разными конфигурациями по умолчанию



##### Полезные ссылки:

[Ссылка №1 на статью:](https://habr.com/ru/companies/nixys/articles/668458/)

[Ссылка №2 на статью](https://habr.com/ru/articles/305400/)