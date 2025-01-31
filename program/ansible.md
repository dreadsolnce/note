
##### **Установка программы**

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

##### Основные команды:

***Пинг хостов :***
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

***Время хостов на сервере:***
```
ansible servers -i hosts.ini -a "date" -k
```

##### Настройка файла инвентаризации

*Создадим в домашнем каталоге отдельный каталог с конфигами:*
```
mkdir ~/ansible.cfg && cd ~/ansible.cfg && touch hosts.ini
```

`vim hosts.ini`

> [!NOTE] Пример файла:
> [servers]
> ubuntu-srv1 ansible_host=192.168.192.11 ansible_port=22  ansible_user=kvl ansible_password=12345678 
> ubuntu-srv2 ansible_host=192.168.192.11 ansible_port=22  ansible_user=kvl ansible_ssh_private_key_file=/kvl/home/.ssh/id_rsa

> [servers] - название группы серверов (любое).
> ansible_port – порт подключения ssh.
> ansible_ssh_private_key_file - путь до закрытого ключа. Для входа без участия пароля.
> ansible_user - имя пользователя для подключения на удаленный сервер.
> ansible_pass - пароль пользователя.


[Ссылка №1 на статью:](https://habr.com/ru/companies/nixys/articles/668458/)