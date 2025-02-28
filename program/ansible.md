
##### Установка программы:

> [!NOTE] Доп. информация
> Для работы Ansible нужно установить на управляющем и управляемых хостах ssh и python.
> 
> Дополнительно, если будет использоваться подключение к управляемым хостам через логин и пароль, а не через ssh ключи, то необходимо установить утилиту sshpass

**server**

```
sudo apt-get install ssh sshpass net-tools ansible python3-pip
```

```
ansible --version
```

```
python --version
```

**client**

```
sudo apt-get install ssh net-tools
```

##### Настройка конфигурации программы ansible:

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

##### Модули:

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

***Модуль ==command==:***

Время хостов на сервере
```
ansible servers -i hosts.ini -m command -a "date" -k
```
тоже что и с участием оболочки shell но без спецсимволов "<", ">", "|", ";", "&"

***Модуль ==shell==***:

Информация о ядре
```
ansible servers -i hosts.ini -m shell -a 'uname -a'
```

```
ansible servers -i hosts.ini -m shell -a 'grep DISTRIB_RELEASE /etc/lsb-release' 
```

***Модуль ==copy== - копирование файла***:
```
ansible ubuntu-srv1 -i hosts.ini -m copy -a 'src=/etc/motd dest=/tmp/ owner=test group=test mode=644'
``` 

> ubuntu-srv1 - запустить только на одном хосте. 

```
ansible ubuntu-srv1 -i hosts.ini -m copy -a "src=/var/ansible/test.txt dest=/var owner=nik group=nik mode=644" -b
```
> -b : если не из под root пользователя, то необходимо добавить -b (sudo) в конце.

***Модуль ==file== - выполняет действие с файлом. Удаляет, изменяет права пользователя и много чего еще***:
	Удаление файла:
```
ansible test -m file -a "path=/var/test.txt state=absent"
```

***Модуль ==apt== - для установки пакетов на удалённом сервере***:
	Установка пакета (state - команда установить, удалить, обновить.):
```
ansible test -m apt -a "name=htop state=latest"
```

***Модуль ==service== - позволяет запускать/останавливать/перезагружать сервисы на удаленном сервере.***:
	Старт сервиса nginx:
```
ansible test -m service -a "name=nginx state=started"
```

***Модуль ==setup== - специализируется на сборе фактов с узлов***:

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

##### Настройка файла инвентаризации:

*Создадим в домашнем каталоге отдельный каталог с конфигами:*
```
mkdir ~/ansible.cfg && cd ~/ansible.cfg && touch hosts.ini
```

`vim hosts.ini`

> [!success] Пример файла
> ``` 
> [servers]
> ubuntu-srv1 ansible_host=192.168.192.11 ansible_port=22  ansible_ssh_user=test_user
> ansible_ssh_password=12345678 ansible_sudo_pass=12345678
> ubuntu-srv2 ansible_host=192.168.192.11 ansible_port=22 ansible_user=<test_user>
> ansible_ssh_private_key_file=/<test_user>/home/.ssh/id_rsa
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

##### Ansible-playbook

**Запуск:**
```
ansible-playbook playbook.yml
```

```
ansible-playbook -i hosts.ini -l ubuntu-srv1 playbook-apache.yml -k
```
> -  k - ввод пароля подключения по ssh (в случае отсутствия параметра ansible_ssh_password)
> -  i  - файл инвентори
> -  l  - ручное указание хоста
> -  t  - запуск начиная с установленного тега 

**Ping:**
```
- name: Test Ping
  hosts: main_server
  become: yes
  tasks:
    - name: ping_to_host
      ping:
```
> Ключи:
1. name - присваивание названия группе/команде.       
2. hosts - указание группы серверов, на которых необходимо выполнить данную команду.    
3. become - запуск sudo (если вы делаете настройки не под root-пользователем).
4. tasks - указываем, что дальше будет идти исполняемая команда.
5. name - присваивание названия команде.
6. ping: - это название модуля, который мы просим использовать.

**Apt:**
```
- name: apache_web
  hosts: main_server
  become: yes
  tasks:
    - name: Installed apache web server
      apt: pkg=apache2 state=latest update_cache=true
```

Результат:
![[Screenshot 2025-02-10 124103.png]]
Описание:
- ``PLAY [web] *********************`` *- ansible говорит нам, что play выполняется в группе `web`. Play — это набор инструкций Ansible, связанных с хостом.*
- ``GATHERING FACTS *********************`` - *перед каждым воспроизведением Ansible запускает модуль setup его на каждом хосте и собирает факты. Если это не требуется (скажем, потому что вам не нужна никакая информация о хосте) можно добавить `gather_facts: no` под строкой хоста (на том же уровне, где находится `tasks:`)*
- ``TASK: [Installs apache web server] *********************`` - *наша первая и единственная задача запущена, и, так как там сказано `changed`, мы знаем, что она изменила что-то на хосте*
- ``PLAY RECAP *********************`` - *наконец, Ansible выводит выжимку того, что произошло: две задачи были выполнены, и одна из них изменила что-то на хосте (это была наша задача apache; модуль setup ничего не меняет).*

###### **Установка apache :**
1.  ***Установка apache через модуль apt***
```
- name: apache_web
		  hosts: servers
  become: yes
  tasks:
# tasks file for apache-proxy

#   - name: Устанавливаем apache с помощью модуля apt
#     apt: pkg=apache2 state=latest update_cache=false
    - name: Обновляем cache установщика apt
      apt: update_cache=false

    - name: Устанавливаем необходимые пакеты apache2, libapache2-mod-php, git
      apt: pkg={{ item }} state=latest
      with_items:
        - apache2
        - libapache2-mod-php
        - git

    - name: Копируем конфигурационный файл
      copy: src=app.conf dest=/etc/apache2/sites-available/ mode=0640
      register: result_copy 

    - name: Проверка отличается ли конф. файл
      debug:
        msg={{ result_copy.changed }}

    - name: Применяем новый конфигурационный файл если он отличается
      command: a2ensite app
      when: result_copy.changed is true 

    - name: Проверяем конфигурацию нового файла и игнорированием ошибок
      # Мы не останавливаем выполнение для дальнейшего возврата к дефолтным настройкам
      command: apache2ctl configtest
      register: result
      when: result_copy.changed is true
      ignore_errors: True

#   - name: Вывод сообщения (переменной) с результатом выполнения команды
#     debug:
#       msg: "My variable = {{ result }}"

    - name: Применяем дефолтный конфигурационный файл если result = failed
      command: a2ensite 000-default
      when: result is failed

    - name: Деактивируем новый файл если result = failed
      command: a2dissite app
      when: result is failed

    - name: Выводим сообщение и заканчиваем выполнение, если result = failed
      fail: msg="Configuration file is not valid. Please check that before re-running the playbook."
      when: result is failed

    - name: Деативируем дефолтный virtualhost
      command: a2dissite 000-default
      when: result_copy.changed is true 


    - name: Деактивируем дефолтный ssl virtualhost
      command: a2dissite default-ssl
      when: result_copy.changed is true 

    - name: Создаем каталог для нового web приложения
      file: dest=/var/www/app mode=755 state=directory

    - name: Клонируем проект (сайт) с github
      git: repo=https://github.com/dreadsolnce/app.git dest=/var/www/app update=no
      tags: deploy

      # Если всё завершилось удачно перезапускаем сервис apache из handlers
      notify:
        - Перезапуск сервиса apache


  handlers:
    - name: Перезапуск сервиса apache
      service: name=apache2 state=restarted


```

> **handlers** - обработчик, который выполняется только при запуске через `notify` директиву. Обработчик выполняется в самом конце playbook, когда все задачи завершены без ошибок. В данную директорию мы помещаем обработчики, которые необходимо запустить в самом конце.
> **tags: deploy** - данный тэг нужен если мы хотим запустить плэйбук с определённого места, 
> `ansible-playbook -i hosts.ini -l ubuntu-srv1 playbook-apache.yml -t deploy`
> будет запущен с места клонирования репозитория.
  
2.  ***Установка haproxy***

```
- hosts: haproxy
  become: yes 
  tasks:
    - name: Обновляем cache apt
      apt: update_cache=false

    - name: Устанавливаем программу haproxy
      apt: 
        pkg: haproxy 
        state: present 
 
    - name: Проверка переменных
      debug:
        msg: "Переменная = {{ hostvars['ubuntu-srv1'] }}"

    - name: Копируем конфигурационный файл программы haproxy
      template: src=templates/haproxy.cfg.j2 dest=/etc/haproxy/haproxy.cfg mode=0640 owner=root group=root
      notify:
        - restart haproxy

    - name: Устанавливаем  default starting flag to 1
      lineinfile: dest=/etc/default/haproxy regexp="^ENABLED" line="ENABLED=1"
      notify:
        - restart haproxy

  handlers:
    - name: restart haproxy
      service: name=haproxy state=restarted
```

> **state: present** - будет установлен пакет (но не обязательно самой последней версии).
> **state: latest** - будет установлен пакет самой последней версии.

   2a.  ***templates/haproxy.cfg.j2***
```
global
    daemon
    maxconn 256

defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

listen cluster
    bind {{ ansible_enp0s8['ipv4']['address'] }}:80
    mode http
    stats enable
    balance roundrobin
{% for backend in groups['servers'] %}
    server {{ hostvars[backend]['ansible_hostname'] }} {{ hostvars[backend]['ansible_enp0s8']['ipv4']['address'] }} check port 80
{% endfor %}
    option httpchk HEAD /index.php HTTP/1.0
```

> `{{ ansible_enp0s8['ipv4']['address'] }}` - переменная берётся из модуля setup,  который запускается в обязательном порядке `GATHERING FACTS`.
> `{{ hostvars[backend]['ansible_hostname'] }} {{ hostvars[backend]['ansible_enp0s8']['ipv4']['address'] }}` - переменная также берется из модуля setup, но только при условии что playbook с `[backend] = (ubuntu-srv1)` также был запущен.
> Пример запуска: `ansible-playbook -i hosts.ini playbook-apache.yml playbook-haproxy.yml`


> [!important] Важно!
> чтобы не запускать два плейбука, можно в файл запускаемого playbook добавить группу      `- hosts: servers` она ни чего делать не будет, а только соберет факты о группе servers (модуль setup). Это необходимо например в том случае если мы изменили/добавили конфигурационный файл для haproxy, а для группы servers мы ни чего не меняли.  
> ``` 
>- hosts: web
>- hosts: haproxy
>   become: yes
>   tasks:
>     - name: Обновляем cache apt
>        apt: update_cache=false
>     - name: Устанавливаем программу haproxy
>        apt: 

##### Ansible Roles:

> Ansible Roles - это роли, позволяющие автоматически загружать связанные переменные, файлы, задачи, обработчики и другие артефакты Ansible на основе известной файловой структуры.

Cоздание структуры директорий:
```
ansible-galaxy init <название>
```

Получаем такую структуру:

```
.└── test    
     ├── defaults    
     │   └── main.yml    
     ├── files    
     ├── handlers    
     │   └── main.yml    
     ├── meta    
     │   └── main.yml    
     ├── README.md    
     ├── tasks    
     │   └── main.yml    
     ├── templates    
     ├── tests    
     │   
     ├── inventory    
     │   └── test.yml
         └── vars
         └── main.yml
```

**defaults** - присваиваются переменные по умолчанию. Данные переменные имеют низкий приоритет. Использоваться будут только в том случае, если до этого данная переменная не была объявлена ранее. В данной директории мы присваиваем defaults-переменные.  
Пример: DOC_ROOT: /var/www/DOMAIN_NAME.com/

**files** - хранятся файлы, которые необходимо будет передать на удаленные сервера с помощью ролей. В данную директорию мы складываем файлы, которые необходимо перенести и использовать на удаленных серверах.  
Пример: В данную директорию помещается код площадки для копирования на удаленный сервер.

**handlers** - это обработчик, который выполняется только при запуске через `notify` -директиву. Обработчик выполняется в самом конце playbook, когда все задачи завершены без ошибок. В данную директорию мы помещаем обработчики, которые необходимо запустить в самом конце.

Пример:

```
handlers:    
  - name: Restart Nginx      
    service: 
        name: nginx  
        state: restarted
```

[meta](https://galaxy.ansible.com/docs/contributing/creating_role.html#role-metadata) - метаданные для роли, включая ролевые зависимости. В данной директории мы указываем так называемые метаданные для проекта. Название компании, название репозиторий, название проекта.

По умолчанию main-файл в данной директории выглядит так:

```
galaxy_info:
  author: your name
  description: your role description
  company: your company (optional)
  license: license (GPL-2.0-or-later, MIT, etc)
  min_ansible_version: 2.1
  galaxy_tags: []
dependencies: []
root@php:/var/ansible/test/meta#
root@php:/var/ansible/test/meta# cat main.yml
galaxy_info:
  author: your name
  description: your role description
  company: your company (optional)
  license: license (GPL-2.0-or-later, MIT, etc)
  min_ansible_version: 2.1
  galaxy_tags: []
dependencies: []
```
**tasks** - основной список задач для Ansible. В данной директории мы указываем все задачи, которые необходимо выполнить на удаленном сервере.  
Пример: установить nginx и открыть 80 порт.

**templates** - в данной директории хранятся файлы шаблоны для развертывания на удаленном сервере.   
Пример: Мы помещаем default страницу nginx и default index.php, после чего передаем на удаленный сервер

**tests** - это название проекта, который был создан при использовании команды `ansible-galaxy init <Название>`. В данной директории будет создан inventory-файл, в котором будут перечислены хосты удаленных серверов. В test.yml помещаются переменные для подключения к удаленному серверу (пользователь, порт и т.д.).

**vars** - аналог defaults. В данной директории присваиваются переменные для запуска ролей. Эта директория имеет приоритет выше, чем defaults.
##### Примеры:
***Запуск определённой роли***
```
roles:  
- { role: role1, tags: role1}  
- { role: role2, tags: role2}  
- { role: role3, tags: role3} 
```
Запуск: `ansible-playbook -i hosts site.yml -t role1`

***Установка пакета apt:***
```
name: "Запрос пакетов, установленных в дистрибутиве"
ansible.builtin.package_facts: manager=auto
```

***Установка программы, если она не установлена:***
```
- name: Устанавливаем программу haproxy
    apt:
      pkg: haproxy
      state: present
    when: "'haproxy' not in ansible_facts.packages"
```

***Копирование файла (шаблона) с созданием копии предыдущего файла:***
```
- name: Копируем новый конфигурационный файл
  template: src=keepalived.conf.j2 dest=/etc/keepalived/keepalived.conf mode=0644 owner=root group=root backup=yes
  become: yes
```

***Debug сообщение стандартного модуля setup:***
```
- name: debug
  debug:
    msg: "{{ ansible_enp0s8['ipv4']['address'] }}"
```

***Debug сообщение вывод объявленной переменной:***
```
- name: "Запуск готового склонированного проекта"
  shell: docker compose up -d 
  register: result
  args:
    chdir: "{{ path_app }}"

- debug:
    msg="{{ result }}"

```

Результат:
![[Screenshot 2025-02-14 162911.png]]

***Ограничить вывод debug сообщения:***
```
- name: "Запуск готового склонированного проекта"
  ...
- debug:
    msg="{{ result.stderr_line }}"
```
![[Screenshot 2025-02-14 163059.png]]

*Debug разделить переменную по ключу и вывести с конца*

```
- name: debug
  debug:
    msg: "{{ ip_hosts.split('.')[-1] }}"
    msg: "{{ ansible_enp0s8['ipv4']['address'].split('.')[-1] }}"
    msg: "{{ ip_hosts | ansible.utils.ipaddr('revdns') }}"
    debug: msg="{{ ip_host.split('.')[0:3] | join('.') }}"


```

*Debug обратить задом на перед вывести каждый элемент начиная со второго*
```
- name: debug
  debug:
    # msg={{ item.ip | ansible.utils.ipaddr('revdns') | split('.') }}
    # msg={{ item.ip.split('.')[0:3] | join('.') | ansible.utils.ipaddr('revdns') | split('.') }}
    msg={{ (item.ip.split('.'))[2::-1] | join('.') }}
  with_items: "{{ hosts }}"

```
(ip=192.168.2.45)
![[Screenshot 2025-02-19 170426.png]]

![[Screenshot 2025-02-19 170357.png]]

Вывод последнего актета ip адреса наоборот
```
- name: debug
  debug:
    msg={{ (item.ip.split('.'))[3:2:-1] | join('.') }}
  with_items: "{{ hosts }}"
```
![[Screenshot 2025-02-20 120423.png]]


*Переменные списки словари:*
Файл с именование переменных:
![[Screenshot 2025-02-19 151852.png]] 
 Файл: j2
```
  {% for item in hosts %}
    {{ item.ip }};
  {% endfor %}

```
 Playbook:
```
- name: debug
  debug:
    msg={{ item.name, item.ip }}
  with_items: "{{ hosts }}"

```
##### Полезные ссылки:

[Ссылка №1 на статью:](https://habr.com/ru/companies/nixys/articles/668458/)

[Ссылка №2 на статью](https://habr.com/ru/articles/305400/)

[Примеры работы с ansible](https://www.dmosk.ru/miniinstruktions.php?mini=ansible-examples)

[Примеры](https://jakondo.ru/ansible-playbook-sozdanie-polzovatelya-v-linux/)

[Debug](https://ru.stackoverflow.com/questions/965368/ansible-%D0%92%D1%8B%D0%B2%D0%BE%D0%B4-%D1%80%D0%B5%D0%B7%D1%83%D0%BB%D1%8C%D1%82%D0%B0%D1%82%D0%B0-%D0%B2%D1%8B%D0%BF%D0%BE%D0%BB%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-%D0%B2-debug)

[Модули](https://ealebed.github.io/posts/2015/%D0%B7%D0%BD%D0%B0%D0%BA%D0%BE%D0%BC%D1%81%D1%82%D0%B2%D0%BE-%D1%81-ansible-%D1%87%D0%B0%D1%81%D1%82%D1%8C-4-%D0%BC%D0%BE%D0%B4%D1%83%D0%BB%D0%B8/)