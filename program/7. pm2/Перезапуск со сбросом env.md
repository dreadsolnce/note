

> [!NOTE] Информация
> Как перезапустить pm2 процесс с принудительным сбросом переменных окружения (.env) файла:
> ```
> cd /var/www/html/dash.ooo/
> pm2 restart ecosystem.config.js --update-env --only rabbit-dash-dossier
> ```
> ___
> Проверяем, что всё ок
> 
> ```
> pm2 env 31 | grep DOSSIER
> ```


***Назад:*** [[program/7. pm2/!------Содержание------!|!------Содержание------!]]
