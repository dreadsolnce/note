### Создание токена доступа с телефона

1. По ссылке [https://github.com/settings/tokens/new](https://github.com/settings/tokens/new) указать:
	- имя токена
	- выбрать: No Expiration (значит токен не устареет)
	- выбрать repo
2. создать токен (зеленая кнопка)
3. сохранить созданный токен
	- GitHub покажет токен вида `ghp_1wKdGc4IhVCHxXEaKClMNp1rEMwsHu1f5zNZ` только один раз, его нужно скопировать и сохранить. Если потеряется - придётся создавать новый.

[см. рисунок](https://habrastorage.org/r/w1560/getpro/habr/upload_files/bce/e82/ac9/bcee82ac94f9fde7344dfd29d058a98e.png)

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

