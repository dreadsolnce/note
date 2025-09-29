***Установка:***

```
pip3 install "molecule molecule_docker molecule_podman
```

```
pip3 install molecule==3.5.2 molecule_docker molecule_podman
```

***Создание роли molecula:***
(На новых версиях molecule данная команда не работает) 

```
molecule init role clickhouse --driver-name docker
```

**Необходимо установить более старую версию molecule при необходимости:**
pip3 install git+https://github.com/ansible-community/molecule-docker.git
```
pip3 install molecule==5.0.1
```

```
pip3 install molecule_docker molecule_podman
```

***Домашнее задание Molecule***
Задание 1:
![[Снимок экрана от 2025-09-28 11-11-39 2.png]]

Задание 2:
![[Снимок экрана от 2025-09-28 11-13-08.png]]
Задание 3:
![[Снимок экрана от 2025-09-28 11-16-28.png]]
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

![[Снимок экрана от 2025-09-27 13-03-52.png]]

```
pip3 install ansible-lint yamllint
```

*Для molecule 5.0.1 необходимо установить определенные версии ansible-lint и yamllint:*

```
pip3 install ansible-compat==3.0.2
pip3 install ansible-lint==5.0.1
ansible-galaxy collection install -vvv --pre community.docker==4.4.0
```

