1. [[#Работа с кластером microk8]]
2. [[#NODE и все что с ними связано]]
3. [[#POD и все что с ними связано]]
4. [[#Deployment]]
5. [[#Service (сеть)]]
6. [[#Логи]]
7. [[#Contoller Ingress]]
8. [[#Volume]]

Просмотр всех доступных api
```
`kubectl api-versions`
```

##### Работа с кластером microk8

***Список плагинов microk8s***

```
microk8s status
```

***Установка плагина storage class (эмитируется nfs хранилище)***

```
microk8s enable storage
```

***Список кластеров***

```
kubectl config get-clusters
```

![[Снимок экрана от 2025-12-10 17-16-30.png]]

***Проверка кластера на наличие ошибок***

```
microk8s inspect
```

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

***Добавление новой nod к кластеру microk8s***

На node на которой запущен microk8s выполнить:

```
microk8s add-node
```

Скопировать команду вида microk8s join 10.129.0.30.....

![[Снимок экрана от 2025-12-16 14-32-29.png]]

Переходим на новую node (должен быть установлен microk8s):

```
ssh -l ubuntu 158.160.27.246 
```

Вставить скопированную команду:

```
microk8s join 10.129.0.30:25000/d2f69e256e702951ad2442b0f644f4c3/1df1fbdafffd
```

![[Снимок экрана от 2025-12-16 14-37-11.png]]

```
kubectl get nodes
```

![[Снимок экрана от 2025-12-16 14-37-58.png]]
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

##### Deployment

***Перезапуск deployment***

```
kubectl rollout restart deployment deploy-front
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

***Просмотр логов контейнера***
```
kubectl logs deploy-app-6d76bd84dc-ds92h
```

***Просмотр логов deployment конкретного контейнера***
```
kubectl logs web-648c987c95-b97kr -c multitool-container
```

```
kubectl logs web-648c987c95-b97kr -c nginx-container
```

***Просмотр логов в реальном времени***
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

##### Volume

***Удалить pvc***

```
kubectl delete persistentvolumeclaims pvc-vol-front
```

***Удалить pv***

```
kubectl delete persistentvolumes
```

***Информация о storage class***

```
kubectl get sc sc-vol
```

***Информация о pvc:***

```
kubectl get pvc
```

***По номеру pvc можем узнать подробную информацию о pv (например где создается каталог)***

```
kubectl describe pv pvc-44a48fbf-9ee0-4319-ad46-ebaa35a96ae
```
###### Пример файла


> [!NOTE] Описание примера
> каталог указанный в path в манифесте pv создается на всех node кластера, но он не является общим ресурсом для всех pod, для каждого pod который запущен на конкретной node он будет своим. 

PV
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol-front
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
#  storageClassName: manual 
  hostPath:
    path: /home/ubuntu/localVolume/front
```

PVC
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-vol-front
spec:
  volumeName: pv-vol-front
#  storageClassName: manual
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

DEPLOYMENT
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name:  deploy-front
  labels:
    app: deploy-web
spec:
  replicas: 6 # Колличество подов 
  selector:
    matchLabels:
      app: deploy-web
  template:
    metadata:
      labels:
        app: deploy-web
    spec:
      containers:
      - name: container-front
        image: nginx:latest
        ports:
        - containerPort: 80
          name: port-nginx
        volumeMounts:
        - name: volume-front
          mountPath: /usr/share/nginx/html/
      volumes:
      - name: volume-front
        persistentVolumeClaim:
          claimName: pvc-vol-front

```


> [!NOTE] Описание примера
> Один из самых простых примеров монтирования каталога без pv и pvc, с помощью локального каталога на node

DEPLOY
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name:  deploy-front
  labels:
    app: deploy-web
spec:
  replicas: 3 # Колличество подов 
  selector:
    matchLabels:
      app: deploy-web
  template:
    metadata:
      labels:
        app: deploy-web
    spec:
      containers:
      - name: container-front
        image: nginx:latest
        ports:
        - containerPort: 80
          name: port-nginx
        volumeMounts:
        - name: volume-front
          mountPath: /usr/share/nginx/html/
      volumes:
      - name: volume-front
        hostPath:
          path: /home/ubuntu/html/front
```


> [!NOTE] Описание использования storage class
> Данный пример  показывает настройку storage class (встроенный в microk7s) и pvc, без использования pv. PV создается автоматически при создании pvc

sc.yml
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: sc-vol
provisioner: microk8s.io/hostpath
reclaimPolicy: Delete
volumeBindingMode: Immediate
```

pvc-front.yml
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-vol-front
spec:
  storageClassName: sc-vol
#  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
```

deployment
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name:  deploy-front
  labels:
    app: deploy-web
spec:
  replicas: 10  # Колличество подов 
  selector:
    matchLabels:
      app: deploy-web
  template:
    metadata:
      labels:
        app: deploy-web
    spec:
      containers:
      - name: container-front
        image: nginx:latest
        ports:
        - containerPort: 80
          name: port-nginx
        volumeMounts:
        - name: volume-front
          mountPath: /usr/share/nginx/html/
      volumes:
      - name: volume-front
        persistentVolumeClaim:
          claimName: pvc-vol-front
# scp -r html ubuntu@158.160.71.158:/home/ubuntu/
```