***Установка molecule:***

```
pip3 install molecule==24.6.0
```

```
pip3 install molecule_podman==2.0.3
```

```
pip3 install molecule_docker==2.1.0
```

***Создание роли molecula:***

```
molecule init role clickhouse --driver-name docker
```

***Установка ролей в playbook-е с помощью ansible-galaxy:***

```
ansible-galaxy install -r requirements.yml -p roles
```

***Домашнее задание Molecule***
Задание 1:
```
cd roles/clickhouse/
```
![[Снимок экрана от 2025-09-29 10-36-35 2.png]]

Задание 2:
```
cd ../../
```

```
cd roles/vector-role
```

```
molecule init scenario docker-vector --driver-name docker
```

![[Снимок экрана от 2025-09-29 10-39-11 2.png]]

**Редактируем файл meta/main.yml:**
*Добавляем role_name и namespace (нельзя использовать знак тире)*

![[Снимок экрана от 2025-09-29 11-17-56 2.png]]

**Запускаем тестирование:**
```
molecule test -s docker-vector
```

***В случае возникновения ошибки необходимо обновить community.docker:***

![[Снимок экрана от 2025-09-29 11-25-33 2.png]]

```
ansible-galaxy collection list | grep docker
```

```
ansible-galaxy collection install community.docker --force
```
 и запустить повторно тестирование

```
molecule test -s docker-vector
```

***СПРАВОЧНО удалить старые версии ansible-galaxy collection (необязательно):*** 
![[Снимок экрана от 2025-09-29 11-43-21 2.png]]
```
rm -r /home/kvl/netology_courses/ansible/venv_python3.11/lib/python3.11/site-packages/ansible_collections/community/docker/
```

```
rm -r /home/kvl/netology_courses/ansible/venv_python3.11/lib/python3.11/site-packages/ansible_collections/community/postgresql/
```

***Добавить нашу роль для тестирования на инстансе quay.io/centos/centos:stream8 из файла molecule.yml, добавив в файл converge.yml нашу роль:***
ВНИМАНИЕ название instance лучше писать с маленькой буквы

![[Снимок экрана от 2025-09-29 11-51-28 3.png]]
![[Снимок экрана от 2025-09-29 11-51-49 2.png]]

***Тестирование упало с ошибкой на centos:stream8:***

![[Снимок экрана от 2025-09-29 11-52-48 2.png]]

***Попробуем протестировать на Fedora 35 (на которую мы устанавливали vector из предыдущих уроков) добавив еще один instance в файл molecule.yml***

![[Снимок экрана от 2025-09-29 12-09-51 2.png]]

![[Снимок экрана от 2025-09-29 12-17-31 2.png]]
Видим, что handler перезапуска службы упал с ошибкой, при этом скачивание и установка пакета прошли успешно!





***tox***
**Установка tox**
```
pip3 install tox
```





------------------------------------------------------------------------

Задание 3:
![[Снимок экрана от 2025-09-28 11-16-28 2.png]]
```
molecule test
```



***Правила тестирования:***

*Создание сценария тестирования:*
(выполняется внутри роли)

```
molecule init scenario default --driver-name docker
```

*vim molecule/default/molecule.yml*

![[Снимок экрана от 2025-09-27 13-03-52 2.png]]

```
pip3 install ansible-lint yamllint
```

*Для molecule 5.0.1 необходимо установить определенные версии ansible-lint и yamllint:*

```
pip3 install ansible-compat==3.0.2
pip3 install ansible-lint==5.0.1
ansible-galaxy collection install -vvv --pre community.docker==4.4.0
```

