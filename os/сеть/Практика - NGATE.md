#--------------------------------------------------------------------------------
|                   Маршруты на хостовой машине Windows:
| # route delete 0.0.0.0
| # route add 0.0.0.0 mask 0.0.0.0 172.16.200.1 metric 50
| route add 10.89.1.20 mask 255.255.255.255 192.168.248.2 metric 10
| netsh interface ipv4 set subinterface "Ethernet 8" mtu=1470 store=persistent
| route print
#--------------------------------------------------------------------------------

#--------------------------------------------------------------------------------
| Добавление маршрута на постоянной основе: route -p add...
| Просмотр сетевых интерфейсов: netsh interface ipv4 show subinterfaces 
#--------------------------------------------------------------------------------


#--------------------------------------------------------------------------------
|                   Маршруты на гостевой машине Astra Linux 1.8: 
| sysctl -w net.ipv4.ip_forward=1
| sudo iptables -t nat -A POSTROUTING -o tun0 -j MASQUERADE
| sudo iptables -A FORWARD -i enp0s8 -o tun0 -j ACCEPT
| sudo iptables -L --line-numbers
#--------------------------------------------------------------------------------

#--------------------------------------------------------------------------------
|            Сохранение маршрутов после перезагрузки в Linux
| sudo iptables-save -f /etc/iptables-conf/iptables_rules.ipv4
| sudo vi /etc/network/if-pre-up.d/iptables
|  #!/bin/sh
|  /sbin/iptables-restore < /etc/iptables-conf/iptables_rules.ipv4
| sudo chmod +x /etc/network/if-pre-up.d/iptables
#--------------------------------------------------------------------------------

***Назад:*** [[os/сеть/!------Содержание------!]]