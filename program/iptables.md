
*Посмотреть список правил:*
```
iptables --line-numbers -L -v -n
```

*Удалить правило 4 в цепочке INPUT:*
```
iptables -D INPUT 4
```
*Открыть порты разом:*
```
iptables -A INPUT -p tcp -m multiport --dports 22,80,443 -j ACCEPT
```
*Открыть порт:*
```
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```
*Закрыть порт:*
```
iptables -A INPUT -p tcp --dport 21 -j DROP
```
*Оставить доступ только одному клиенту по SSH, остальным запретить все:*
```
iptables -A INPUT -p tcp ! -s 192.168.124.5 --dport 22 -j DROP
```
> [!NOTE] Пояснение
> Здесь _192.168.124.5_ — IP-адрес доверенной машины, знак _!_ перед ключом _-s_
>  используется для инверсии, т.е. всем, кроме этого адреса доступ закрыт.

[Ссылка №1 на статью](https://selectel.ru/blog/setup-iptables-linux/)