***Установка***

Через пакетный менеджер pip:

`pip3 install ansible --user`

Через переменные окружения python:

`python3 -m venv .venv`

Создание окружения python определенной версии:

curl https://pyenv.run | bash

```
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
```

```
source ~/.bashrc
```

Просмотр доступных версий python:

``pyenv install --list``

Перейти в каталог проекта и установить для проекта нужную версию:

```python
sudo apt-get install libssl-dev 
```

```python
sudo apt-get install liblzma-dev
```

```python
sudo apt-get install python3-tk
```

```python
sudo apt-get install libsqlite3-dev
```

```python
sudo apt install libreadline-dev
```

```python
sudo apt-get install libffi-dev
```

```python
sudo apt-get install -y libncurses5-dev libncursesw5-dev
```

```python
sudo apt-get install libbz2-dev
```

``pyenv install 3.7.0``

`pyenv local 3.7.0`

`pyenv versions`

Создаем окружение:

```
pip3.9 install --upgrade pip
```

```
pip install env
```

`python3 -m venv .venv`

`source .venv/bin/activate`

`pip install --upgrade pip`

`pip3.7 install ansible --user`

`deactivate`

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
￼￼

ss
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