***Установка***

Через пакетный менеджер pip:

`pip3 install ansible --user`

***Запуск***

Запуск простейшего модуля:

`ansible -m ping localhost`

Запуск playbook:

`ansible-playbook -i inventory/hosts.yml site.yml`

***Работа с inventory файлом:***

Просмотр групп и хостов в inventory файле:

`ansible-inventory -i inventory/hosts.yml --graph`

Определение с какими параметрами будет запускаться ansible для каждого хоста:

`ansible-inventory -i inventory/hosts.yml --list`

Информация о конкретном хосте:

`ansible-inventory -i inventory/hosts.yml --host centos8`

***Facts***

Получить facts с хоста:

`ansible hostname -m setup`

Facts хранятся в переменной ansible_facts

Выключить сбор facts:

gather_facts: false

***Vault***

Зашифровать файл:

`ansible-vault encrypt group_vars/prod/custom.yml`

Запуск с вводом пароля для зашифрованных файлов:

`ansible-playbook -i inventory/hosts.yml site.yml --ask-vault-pass`

Расшифровать файл:

`ansible-vault decrypt group_vars/prod/custom.yml`

Просмотр зашифрованного файла без дешифровки:

`ansible-vault view group_vars/prod/custom.yml`

Редактирование файла без дешифровки:

`ansible-vault edit group_vars/prod/custom.yml`

Шифровать отдельные строки в файле:

`ansible-vault encrypt_string`

-> пишем нужное значение -> копируем то что выдало -> и вставляем зашифрованное значение в файл