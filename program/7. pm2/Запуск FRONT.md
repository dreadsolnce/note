Из папки проекта фронтовой части запустить файл ecosystem.config.cjs из ***под пользователя владельца*** каталога

```
sudo -u user -i
```

```
cd /var/www/html/front.mi.dash.ooo
```

```
ls -l ecosystem.config.cjs
```

```
pm2 start ecosystem.config.cjs --env development --only front.dev
```

![[Снимок экрана от 2025-12-05 12-19-04 1.png]]
![[Снимок экрана от 2025-12-05 12-18-45 1.png]]
![[Снимок экрана от 2025-12-05 12-43-32 1.png]]

> [!NOTE] Пояснение
> Менеджер процессов **pm2** запускает приложение с названием ***front.dev*** с помощью **npm** с аргументами ***run dev***


> [!NOTE] что делает команда npm run dev
> **npm run dev** — это команда для запуска ***сервера разработки*** в проектах на Node.js и JavaScript. Она автоматизирует множество задач, таких как компиляция кода, автоматическая перезагрузка при внесении изменений и отладка, что значительно ускоряет процесс разработки. **Автоматический перезапуск**: Сервер отслеживает изменения в коде и автоматически перезагружает приложение, как только вы сохраняете файл.
> **npm run build** - В отличие от **npm run dev**, который предназначен для разработки, команда **npm run build** создает оптимизированную для ***продакшена*** сборку вашего приложения, готовую к развертыванию.


> [!important] ***Пояснение по поводу ключа --env***
> **В PM2 логика работы с окружениями строится по принципу:**
> * env — это базовые переменные (всегда загружаются по умолчанию).
> * env_имя — это дополнительные переменные, которые активируются только при запуске с флагом --env имя.

***Разбор на примере кода:***

```
module.exports = {  
   apps : [{  
     name: "my-app",  
     script: "./app.py",  
     // Стандартные переменные  
     env: {  
       NODE_ENV: "base"  
     },  
     // Переменные для разработки (убирает WARN)  
     env_development: {  
       NODE_ENV: "development",  
       DEBUG: "true"  
     },  
     // Переменные для продакшена  
     env_production: {  
       NODE_ENV: "production",  
       DEBUG: "false"  
     }  
   }]}
```
***Резюме по командам:***
   1. pm2 start ecosystem.config.js — загрузит env. (Может выдать WARN, если ищет default dev).
   2. pm2 start ecosystem.config.js --env development — загрузит env + env_development.
   3. pm2 start ecosystem.config.js --env production — загрузит env + env_production.

***Совет:*** Чтобы предупреждение исчезло, просто добавьте пустой объект env_development: {} в конфиг, если вам не нужны там никакие особые переменные.

Примеры запуска:
```
pm2 start ecosystem.config.js --env development --only my-python-app
```

```
pm2 start ecosystem.config.js --env production --only my-python-app
```

```
pm2 start ecosystem.config.js --only my-python-app
```

![[Снимок экрана от 2026-05-15 15-37-08.png]]

***Назад:*** [[program/7. pm2/!------Содержание------!|!------Содержание------!]]
