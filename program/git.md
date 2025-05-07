### Создание токена доступа с телефона

1. По ссылке [https://github.com/settings/tokens/new](https://github.com/settings/tokens/new) указать:
	- имя токена
	- выбрать: No Expiration (значит токен не устареет)
	- выбрать repo
2. создать токен (зеленая кнопка)
3. сохранить созданный токен
	- GitHub покажет токен вида `ghp_1wKdGc4IhVCHxXEaKClMNp1rEMwsHu1f5zNZ` только один раз, его нужно скопировать и сохранить. Если потеряется - придётся создавать новый.

[см. рисунок](https://habrastorage.org/r/w1560/getpro/habr/upload_files/bce/e82/ac9/bcee82ac94f9fde7344dfd29d058a98e.png)

### Инициализация (создание) нового репозитория

1. В директории проекта выполнить команду:
```
git init
```

> [!NOTE] Замечание
> - По умолчанию git создаёт ветку master, где будут размещаться коммиты. В gitHUB же дефолтная ветка называется main — несоответствие веток может привести к проблемам в будущем, лучше сразу переименовать ветку master в main;
> - Задать имя дефолтной ветки можно с помощью команды git config --global init.defaultBranch main, где main — название ветки. Эта команда работает до создания первого коммита;
> - Переименовать уже существующую ветку можно командой git branch -m [название ветки].

2. Задаём название дефолтной ветки для следующих всех проектов
```
git config --global init.defaultBranch main
```

3. Меняем название текущей (гоавной) ветки
```
git branch -m main
```

```
git status
```

4. Создаем можно пустой файл README.md
```
touch README.md
```

5. Добавляем все файлы в индекс
```
git add .
```

6. Проверяем
```
git status
```

7. На сайте создаем пустой (чистый репозиторий) репозиторий

8. Создаём (прописываем) новую удалённую ветку (origin) в локальном репозитории
```
git remote add origin git@gitlab.test.local:kvl/myfirstproject.git
```
либо
```
git remote add origin  http://gitlab.test.local/kvl/myfirstproject.git
```


> [!NOTE] Замечание
> если использовать url вида git@gitlab... то предваоительно надо настроить авторизацию по ssh ключу

9. Проверяем
```
git remote -v
```

10. Коммитим наши изменения
```
git commit -m "Initial commit"
```

10. Пушим в удалённый репозиторий
```
git push --set-upstream origin main
```

11. Проверяем на сервере наш репозиторий


### Настройка новой ветки которую создали в gitlabe

Для того чтобы появилась новая ветка необходимо сделать 

```
git pull
git branch -a
```

### Подключение удалённого репозитория с токеном

```
git remote -v
git remote remove origin
git remote add origin https://ghp_***@github.com/dreadsolnce/note.git
git remote -v
git push origin main
```

### Создание ветки (branch)

*Создание новой ветки:*
```
git branch <name>
```

*Просмотр всех веток:*
```
 git branch -a
```

 *Переключение на ветку:*
```
 git checkout <name>
```

 *Удалить локальную ветку:*
```
  git branch -d <name>
```
[^1]

 *Удалить удалённую ветку из репозитория:*
```
  git push origin :<name remote>
  git branch -rD <name remote>
```
[^2]

[^1]: git branch -d ansible

[^2]: git push origin :origin/ansible
git branch -rD origin/ansible

### Объединить ветки

*Просмотр всех веток:*
```
 git branch -a
```

*Переключение на ветку, в которую вы хотите объединить изменения::*
```
 git checkout main
```

Выполните команду `git merge`, указав ветку, изменения из которой вы хотите объединить:
```
git merge bash
```

### Слить изменения из GitLab

*Переключиться на главную ветку:*
```
git checkout main
```
![[Снимок экрана от 2025-04-07 14-58-07.png]]
*Выполнить загрузку из репозитория GitLab*
```
git pull
```

### Включение HTTPS

- ***Установить программное обеспечение***
```
sudo apt install certbot
```
	
	Для веб-сервера Apache установить плагин
```
sudo apt install python3-certbot-apache
```
- ***Установить сертификат TLS***
```
sudo certbot --apache --agree-tos --redirect --hsts --staple-ocsp --must-staple --email [email]  -d tile.your-domain.com
```


> [!NOTE] Внимание
> При появлении ошибки AttributeError: module 'certbot.plugins.common' has no attribute 'TLSSNI01'
> можно попробовать способ представленный ниже


- Отредактировать файл index.html и заменить весь протокол HTTP на HTTPS
```
sudo sed -i 's/http/https/g' /var/www/index.html
```

##### Ссылки на сайты

[# Как объединить ветки в Git](https://sky.pro/wiki/javascript/kak-obuedinit-vetki-v-git/)

