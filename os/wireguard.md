[Ссылка](https://blog.sedicomm.com/2021/02/19/kak-legko-zapustit-svoj-server-vpn-ispolzuya-wireguard/)

`cd ~

`curl -O https://raw.githubusercontent.com/angristan/wireguard-install/master/wireguard-install.sh

`chmod +x wireguard-install.sh

`./wireguard-install.sh

![[Снимок экрана от 2025-06-05 15-35-29.png]]

![[Снимок экрана от 2025-06-05 15-36-46.png]]

Скопировать себе на устройство файл c именем которое мы указывали (wg0-client-phone_my_cli_01.conf)

**Установка на клиент с linux:**

на сервере запустить wireguard-install.sh и создать новый конфигурационный файл:
![[Снимок экрана от 2025-08-06 12-19-07.png]]

на клиенте:

```
sudo apt install wireguard

sudo su

cd /et/wireguard

vim client_02.conf

=> вставить содержимое файла с сервера

=> запустить сетевой интерфейс

wg-quick up client_02

```

***На телефоне прописать dns сервер:***

```
208.67.222.222, 1.1.1.1
```
