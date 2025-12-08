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

Запуск пода из файла конфигурации
```
kubectl apply -f nginx-test.yaml
```

Информация о всех подах в том числе и системных 
```
kubectl get pods -A
```

Информация о созданных подах
```
kubectl get pods
```

Информация о подах принадлежащих одному пространству имен
```
kubectl get pods -n default
```
где default - это имя namespace

Удаление пода
```
kubectl delete pod <имя-пода> -n <namespace>
```

Проброс порта для подключения локально

```
kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443
```

Просмотр сетевых подключений
```
kubectl get service
```

