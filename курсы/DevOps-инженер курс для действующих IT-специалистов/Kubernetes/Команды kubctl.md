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

Пробросить порт для подключения локально

```
kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443
```

Просмотр сетевых подключений
```
kubectl get service
```

Подробная информация о сервисе
```
kubectl describe services/service-nginx
```

Пробросить порт сервиса
```
 kubectl port-forward svc/service-nginx 30000:80
```

Информация о созданных подах
```
kubectl get pods
```

Информация о подах принадлежащих одному пространству имен
```
kubectl get pods -n default
```
***где default - это имя namespace***

Информация о подах по label
```
kubectl get pods -l app=web
```

Информация о всех подах в том числе и системных 
```
kubectl get pods -A
```

Подробная информация о pod-е
```
kubectl describe pod nginx
```
Подключиться к pod-у
```
kubectl exec -it nginx -- bash
```

Удаление пода
```
kubectl delete pod <имя-пода> -n <namespace>
```

Просмотр логов контейнера
```
kubectl logs deploy-app-6d76bd84dc-ds92h
```

Просмотр логов deployment конкретного контейнера
```
kubectl logs web-648c987c95-b97kr -c multitool-container
```

```
kubectl logs web-648c987c95-b97kr -c nginx-container
```

