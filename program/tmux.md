Терминал слева        ctrl + b _ ]
Терминал снизу        ctrl + b _ "
Закрыть сессию        ctrl + b _ d

***Создание новой сессии:
```
tmux new -s osmDownload
```
Просмотр запущенных сессий
```
tmux ls
```
Восстановить сессию
```
tmux attach -t osmDownload
```

***Удалить все сессии***
```
tmux kill-server
```
___

> [!NOTE] ВАЖНО
> Во многих дистрибутивах, в частности astra linux, systemd прибивает пользовательские процессы при выходе из сессии. Регулируется параметром:
> ***KillUserProcesses***
> в файле `/etc/systemd/logind.conf`
> По умолчанию имеет значение **yes**. Нужно прописать
> ***KillUserProcesses=no***
> и перезагрузить systemd-logind  
> systemctl restart systemd-logind

***Подключение через tmux:***
*На клиенте:*
```
tmux ls
```

```
tmux new -s localOSM
```
или
```
tmux attach -t localOSM
```

```
./bash_project/connection.sh
```

```
tmux ls
```

```
tmux new -s remoteOSM
```
или
```
tmux attach -t remoteOSM
```
