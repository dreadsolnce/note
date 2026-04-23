***Запуск sudo без пароля:***

```
sudo visudo -f /etc/sudoers.d/nopass
```

```
kvl    ALL=(ALL)    NOPASSWD: ALL
```

![[Pasted image 20260416114806.png]]

```
sudo chmod 440 /etc/sudoers.d/nopass
```



