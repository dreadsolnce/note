Список кластеров  
```
kubectl config get-clusters
```

Список пользователей
```
kubectl config get-users
```

Список контекстов
```
kubectl config get-contexts
```

Текущий контекст
```
kubectl config current-context
```

Переключение текущего контекста
```
kubectl config use-context name
```

Установка доступов
```
kubectl config set-credentials
```

Более подробная информация о нодах
```
kubectl get nodes -o wide
```

Информация очень подробная о нодах 
```
kubectl describe nodes
```

Информация о подах
```
kubectl get pods -A
```

