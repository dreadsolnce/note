1. [[#Работа с кластером microk8]]
2. [[#NODE и все что с ними связано]]
3. [[#POD и все что с ними связано]]
4. [[#Service (сеть)]]
5. [[#Логи]]
6. [[#Contoller Ingress]]

Просмотр всех доступных api
```
`kubectl api-versions`
```

##### Работа с кластером microk8

***Список кластеров***

```
kubectl config get-clusters
```

![[Снимок экрана от 2025-12-10 17-16-30.png]]

***Список пользователей***

```
kubectl config get-users
```

![[Снимок экрана от 2025-12-10 17-35-37.png]]

***Список контекстов***

```
kubectl config get-contexts
```

![[Снимок экрана от 2025-12-10 17-36-48.png]]

***Текущий контекст***

```
kubectl config current-context
```

![[Снимок экрана от 2025-12-10 17-38-07.png]]

***Переключение текущего контекста***
```
kubectl config use-context name
```

***Установка доступов***
```
kubectl config set-credentials
```

##### NODE и все что с ними связано

> [!NOTE] Что такое node
> node - это виртуальный сервер, реальный сервер

***Информация о nod-ах в компактом виде***

```
kubectl get nodes -o wide
```

![[Снимок экрана от 2025-12-10 17-46-19.png]]

***Очень подробная Информация о nod-ах***

```
kubectl describe nodes
```

![[Снимок экрана от 2025-12-10 17-44-26.png]]

##### POD и все что с ними связано

***Запуск пода из файла конфигурации***

```
kubectl apply -f pod-nginx.yaml
```

![[Снимок экрана от 2025-12-10 18-09-54.png]]

***Информация о запущенных подах в namespace по умолчанию***

```
kubectl get pods
```

![[Снимок экрана от 2025-12-10 18-12-21.png]]

***Информация о всех запущенных подах в namespace по умолчанию***

![[Снимок экрана от 2025-12-10 18-13-11.png]]

***Информация о подах принадлежащих одному пространству имен***
```
kubectl get pods -n default
```
***где default - это имя namespace***

![[Снимок экрана от 2025-12-10 18-14-51.png]]

***Информация о подах по label***
```
kubectl get pods -l app=web
```

![[Снимок экрана от 2025-12-10 18-16-51.png]]

***Подробная информация о pod***
```
kubectl describe pod nginx
```

![[Снимок экрана от 2025-12-10 18-18-24.png]]

***Подключиться (зайти внутрь контейнера) к pod***

```
kubectl exec -it pod-nginx -- bash
```

![[Снимок экрана от 2025-12-10 18-19-55.png]]

***Удаление пода***

```
kubectl delete pod <имя-пода> -n <namespace>
```

##### Service (сеть)

> [!NOTE] Для чего нужен
> С помощью данной сущности можно оперировать доменными именами, он имеет постоянный ip адрес в отличие от pod

***Просмотр сетевых подключений***

```
kubectl get service
```

![[Снимок экрана от 2025-12-10 18-23-55.png]]

***Подробная информация о сервисе***

```
kubectl describe services/service-web-nginx-busybox
```

```
kubectl describe svc service-web-nginx-busybox
```

![[Снимок экрана от 2025-12-10 18-26-31.png]]

***Пробросить порт сервиса***
```
 kubectl port-forward svc/service-nginx 30000:80
```

***Пробросить порт для подключения c локальной машины с указанием namespace***

```
kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443
```

![[Снимок экрана от 2025-12-10 18-28-53.png]]

##### Логи

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

Просмотр логов в реальном времени
```
kubectl logs -f web-648c987c95-b97kr
```
##### Contoller Ingress

***Информация о контроллере***
```
kubectl describe ingress ingress-web 
```

![[Снимок экрана от 2025-12-13 13-30-09.png]]

***Просмотр классов контроллера***
```
kubectl get ingressclasses.networking.k8s.io
```

![[Снимок экрана от 2025-12-13 13-31-49 1.png]]

***Просмотр контроллера***
```
kubectl get ingress
```

![[Снимок экрана от 2025-12-13 13-31-11.png]]
###### Пример файла
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-web
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: "app.test.com"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: service-web-front
            port:
              number: 80
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: service-web-back
            port:
              number: 80
  - host: "app2.test.com"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: service-web-back
            port:
              number: 80

```
