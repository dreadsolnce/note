[Ссылка на ресурс:](https://interface31.ru/tech_it/2022/10/nastraivaem-otkazoustoychivyy-dns-server-na-baze-bind-9.html)

## Проверка работы dns:

##### Проверка работы обратной зоны
```
nslookup 192.168.192.101
```

![[Снимок экрана от 2025-03-27 16-46-10 3.png]]

##### Проверка работы прямой зоны
```
nslookup ai-app1p.test.local
```

![[Снимок экрана от 2025-03-27 17-16-32.png]]

## Настройка systemd-resolved для локального кэширования

[Ссылка на ресурс](https://zevilz.dev/posts/496/)

##### Проверка текущих настроек резолвера

```
resolvectl status
```

![[Снимок экрана от 2025-03-27 17-26-00.png]]

