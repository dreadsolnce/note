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



