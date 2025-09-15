Через пакетный менеджер pip:

`pip3 install ansible --user`

Запуск простейшего модуля:

`ansible -m ping localhost`

Запуск playbook:

`ansible-playbook -i inventory/hosts.yml site.yml`

Работа с inventory файлом:

Просмотр груп и хостов в inventory файле:

`ansible-inventory -i inventory/hosts.yml --graph`

Определение с какими параметрами будет запускаться ansible для каждого хоста:

`ansible-inventory -i inventory/hosts.yml --list`

Информация о конкретном хосте:

`ansible-inventory -i inventory/hosts.yml --host centos8`

Facts






