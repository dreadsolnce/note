***Проверить наличие swap:***

```
sudo swapon --show
```

***Посмотреть текущее значение:***

```
cat /proc/sys/vm/swappiness
```

***Временно изменить значение (до перезагрузки):***

```
sudo sysctl vm.swappiness=10
```

***Сохранить навсегда:***

**Отредактируйте файл /etc/sysctl.conf и добавьте или измените строку:**

```
sudo vim /etc/sysctl.conf
```

```
+++ vm.swappiness=10
```