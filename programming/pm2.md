![[Screenshot_24.png]]

Запуск pm2:

На такой запуск жаловался Игорь. Постоянно перезагружается
```
pm2 start ecosystem.config.cjs --env development --only front.dev --watch
```

Простой запуск:
```
pm2 start ecosystem.config.cjs --env development --only front.dev --watch
```

Просмотр логов:

```
pm2 logs
```