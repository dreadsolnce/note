***--НАСТРОЙКА СЕРВЕРА:--***
___

> [!important] ВАЖНО
> Перед установкой у вас должно быть зарегистрировано доменное имя.

1. **Автоматически обновляем систему:**

```
apt update -y && apt upgrade -y
```

2. **Включаем Google BBR для повышения эффективности обмена трафиком (без этого работать будет медленно):**

```
echo "net.ipv4.tcp_congestion_control=bbr" | sudo tee -a /etc/sysctl.conf
echo "net.core.default_qdisc=fq" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

3. **Ставим Go последней версии не из репозиториев Debian, так как там версия Go устаревшая:**

```
wget https://go.dev/dl/go1.26.2.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.26.2.linux-amd64.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.profile
source ~/.profile
```

4. **Качаем исходники модифицированного Caddy с плагином forwardproxy:**

```
go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest
```

5. **Cобираем Caddy:**

```
~/go/bin/xcaddy build --with github.com/caddyserver/forwardproxy@caddy2=github.com/klzgrad/forwardproxy@naive
```

6. **Создаём каталог для файла конфига Caddy:**

```
sudo mkdir /etc/caddy
sudo touch /etc/caddy/Caddyfile
```

7. **Генерируем случайное имя пользователя и пароль:**

```
apt install openssl -y # ставим openssl если он не стоит
openssl rand -base64 64 | tr -dc 'A-Za-z0-9' | head -c 16; echo # логин
openssl rand -base64 64 | tr -dc 'A-Za-z0-9' | head -c 16; echo # пароль
```

8. **Создаем конфигурационный файл Caddyfile:**

```text
cat <<EOF > /etc/caddy/Caddyfile
:443, your-domain.com
tls example@example.com

route {
 forward_proxy {
   basic_auth user pass
   hide_ip
   hide_via
   probe_resistance
 }

 reverse_proxy https://demo.cloudreve.org {
   header_up Host {upstream_hostport}
   header_up X-Forwarded-Host {host}
 }
}
EOF
```

- `your-domain.com` на купленный домен;
- `example@example.com` на свою почту (на неё зарегистрируется сертификат);
- `user` и `pass` на свои логин и пароль из предыдущего шага;
- `https://demo.cloudreve.org` меняем на любой сайт, под который хотим маскироваться. Например: https://en.wikipedia.org Также он будет доступен при прямом переходе по твоему домену.

9. **Проверить конфигурационный файл на наличие ошибок:**

```
sudo  caddy validate --config /etc/caddy/Caddyfile
```

10. **Перемещаем собранный бинарник Caddy в пользовательский каталог:**
```
sudo mv caddy /usr/bin/caddy
sudo chmod +x /usr/bin/caddy
```

11. **Создаём systemd юнит для автоматического перезапуска:**

```
sudo touch /etc/systemd/system/caddy.service 
```

```
cat <<EOF | sudo tee /etc/systemd/system/caddy.service
[Unit]
Description=Caddy with NaiveProxy
After=network.target network-online.target
Requires=network-online.target

[Service]
Type=notify
User=root
Group=root
ExecStart=/usr/bin/caddy run --environ --config /etc/caddy/Caddyfile
ExecReload=/usr/bin/caddy reload --config /etc/caddy/Caddyfile --force
TimeoutStopSec=5s
LimitNOFILE=1048576
LimitNPROC=512
PrivateTmp=true
ProtectSystem=full
AmbientCapabilities=CAP_NET_BIND_SERVICE
Restart=always
RestartSec=5s

[Install]
WantedBy=multi-user.target
EOF
```

*Он автоматически получит TLS-сертификат на указанную почту*

12. **Запускаем Caddy. Он автоматически получит TLS-сертификат**

```
sudo systemctl daemon-reload
sudo systemctl enable --now caddy
```

___

***--НАСТРОЙКА КЛИЕНТА НА ANDROID--***

1. ***Скачать и установить клиент apk с сервера github:***

**URL:** https://github.com/MatsuriDayo/NekoBoxForAndroid/releases
**ВЕРСИЯ:** [NekoBox-1.4.2-arm64-v8a.apk]

2. ***По умолчанию протокол Naive в программе не установлен:***

- Необходимо нажать +
- Выбрать Naive
- И программа сама предложит ссылку на скачивание плагина

3. ***Создать (импортировать)новое подключение:***

- Импорт из буфера обмена (скопировать строку в буфер):

naive+https://user:12345678@test.ru:11443?sni=test.ru#Caddy-Naive

- Создать новое подключение:

`+ -> Ручные настройки -> Naive`

```
Имя профиля: любое понятное имя
Сервер: ip адрес сервера
Удаленный порт: порт на сервере (из конфига)
Имя пользователя: username
Пароль: password
Протокол: HTTPS
SNI: доменное имя (test.ru либо test.test.ru)
```

___

***--ПРИМЕР МОЕГО ФАЙЛА--***

***Зарубежный сервер***

> [!NOTE] Дополнение
> Мой файл использует нестандартный порт, в связи с чем caddy автоматически не может выпустить сертификаты LetsEnCrypt (т.к. для выпуска сертификатов необходимы стандартные порты 80 и 443), то в файле конфига используются пути к файлам сертификата, которые в свою очередь выпускаются через nginx.

![[Снимок экрана от 2026-04-14 16-25-34.png]]

***Российский сервер посредник - весь трафик целиком перенаправляется***

![[Снимок экрана от 2026-04-17 12-00-25.png]]

***--НАСТРОЙКА КЛИЕНТА НА LINUX--***

1. Скачать архив с клиентом

https://github.com/klzgrad/naiveproxy/releases

2. Распаковать архив

```
tar -xf Загрузки/naiveproxy-v147.0.7727.49-1-linux-x64.tar.xz -C ~
```

3. Скопировать исполняемый файл в систему

```
sudo cp ~/naiveproxy-v147.0.7727.49-1-linux-x64/naive /usr/local/bin/
```

4. Удалить разархивированный каталог

```
rm -r naiveproxy-v147.0.7727.49-1-linux-x64/
```

5. Создать конфигурационный файл

```
sudo mkdir -p /usr/local/etc/naive/
```

```
sudo vim /usr/local/etc/naive/config.json
```

```
{
    "listen": "http://127.0.0.1:3080",
    "proxy": "https://<username>:<password>@kvlpro.space:44443",
    "padding": true
}
```

6. Запуск клиента

```
naive /usr/local/etc/naive/config.json
```

7. Проверка

```
export http_proxy=http://127.0.0.1:3080; export https_proxy=http://127.0.0.1:3080;
```

```
curl ifconfig.me; echo ; curl 2ip.ru
```

8. Создать службу systemd для клиента

```
sudo vim /etc/systemd/system/naive-client.service
```

```
[Unit]
Description=Xray Client Service
After=network.target

[Service]
Type=simple
User=kvl
ExecStart=/usr/local/bin/naive /usr/local/etc/naive/config.json
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

9. Запуск службы:

```
sudo systemctl daemon-reload && sleep 5 ; sudo systemctl enable --now naive-client; sleep 5 && curl -x http://127.0.0.1:3080 ifconfig.me ; echo ; curl -x http://127.0.0.1:3080 2ip.ru
```



### Тестовая настройка проксирования трфика



cd /usr/local/share/xray
sudo wget https://github.com/v2fly/domain-list-community/releases/latest/download/dlc.dat -O geosite.dat
sudo wget https://github.com/v2fly/geoip/releases/latest/download/geoip.dat -O geoip.dat
sudo chmod 644 /usr/local/share/xray/geosite.dat
sudo chmod 644 /usr/local/share/xray/geoip.dat



wget https://github.com/SagerNet/sing-box/releases/download/v1.13.8/sing-box_1.13.8_linux_amd64.deb

sudo dpkg -i sing-box_1.13.8_linux_amd64.deb

sing-box version

sudo cp cat  /etc/sing-box/cert.pem
sudo cp /root/.local/share/caddy/certificates/acme-v02.api.letsencrypt.org-directory/kvlpro.space/kvlpro.space.key /etc/sing-box/key.pem

