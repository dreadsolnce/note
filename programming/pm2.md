![[Screenshot_24.png]]

***Запуск pm2:

*На такой запуск жаловался Игорь. Постоянно перезагружается
```
pm2 start ecosystem.config.cjs --env development --only front.dev --watch
```

*Простой запуск:
```
pm2 start ecosystem.config.cjs --env development --only front.dev --watch
```

***Просмотр логов:

```
pm2 logs
```


> [!NOTE] Title
> PM2 в основном предназначен для приложений Node.js, но работает и с остальными языками программирования. Чтобы запустить приложение, используем команды в консоли.  
  
*Для Node.js:

pm start app_name.js

*Для остальных языков программирования (на примере Python 3):

pm2 start app_name.py --interpreter=python3


> [!quote] Title
> Если ваше приложение завершит работу с ошибкой, PM2 автоматически перезапустит его, что очень удобно.

