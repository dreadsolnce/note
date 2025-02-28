### Создание токена доступа с телефона

1. По ссылке [https://github.com/settings/tokens/new](https://github.com/settings/tokens/new) указать:
	- имя токена
	- выбрать: No Expiration (значит токен не устареет)
	- выбрать repo
2. создать токен (зеленая кнопка)
3. сохранить созданный токен
	- GitHub покажет токен вида `ghp_1wKdGc4IhVCHxXEaKClMNp1rEMwsHu1f5zNZ` только один раз, его нужно скопировать и сохранить. Если потеряется - придётся создавать новый.

[см. рисунок](https://habrastorage.org/r/w1560/getpro/habr/upload_files/bce/e82/ac9/bcee82ac94f9fde7344dfd29d058a98e.png)

### Инициализация нового проекта

- Перейти в каталог с проектом (пустым либо там уже что то есть)
```
git init
```
- Создаём файл .gitignore
```
vim .gitignore
```
- Добавить изменения в индекс
```
git add <filename>
или
git add -A # добавиьт все
или
git add .  # добавить все
```
- Создать первый коммит:
```
git commit -m "first commit"
```
- Создать удалённую ветку 
```
git remote add origin https://github.com/dreadsolnce/awesome-app.git
```
- Создаем ветку master
```
git branch -M master
```
- Создаем каталог (проект) awesome-app на сайте github

![[Screenshot 2025-02-11 145200.png]]

- Запушиваем наш проект на git hub
```
git push -u origin master
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

[^3]: 
