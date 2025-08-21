
**_Создание сети и под сети внутри сети:_**

1. Создание сети

```
yc vpc network create --name net --labels my-label=netology --description "My network"
```

2. Создание под сети:

```
yc vpc subnet create --name my-subnet-a --zone ru-central1-a --range 10.1.2.0/24 --network-name net --description "My subnet"
```