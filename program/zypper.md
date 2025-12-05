***Вывести все доступные пакеты***

````
zypper se 
````

***Вывести установленные пакеты***

```
zypper se -i
```

***Проверить, установлен ли пакет точно по имени:***
```
zypper search --installed-only --match-exact имя_пакета
```

***Вывести подробную информацию об установленном пакете***
```
sudo zypper info zabbix-agent2
```

***Загрузить пакет из репозитория:***
```
zypper download python3-rpm
```

***Добавить репозиторий***
```
sudo zypper addrepo -f -n 'MosOS addon' https://mos-repo.mos.ru/mosos/arbat/15.5/repo/addon/ addon
```
> [!NOTE] описание
> -f : автоматическое обновление репозитория
> -n : имя реплитория


***Просмотр репозитариев***
```
sudo zypper repos
```
![[Снимок экрана от 2025-11-07 18-28-34.png]]

***Удаление репозитория из списка***

```
sudo zypper removerepo postgres17
```

