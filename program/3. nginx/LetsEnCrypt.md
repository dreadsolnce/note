**Выпуск сертификата LetsEnCrypt c помощью certbot**

```
sudo apt install certbot python3-certbot-nginx
```

***Выпускаем сертификат:***

```
sudo certbot certonly -d kvlpro.ru -d www.kvlpro.ru
```

> [!important] Замечание
> Перед выпуском сертификата не должно быть директивы ssl_certificate с указанием путей к ключам, а именно блока server
> и открыт порт 80 на firewall

![[Pasted image 20260414131615.png]]

***Добавляем сертификаты в конфигурационный файл сайта:***

**1 вариант: напрямую в блок server:**

![[Pasted image 20260414131707.png]]

***2 вариант: через специальный файл snippets/ssl-wild.conf:***

```
sudo vim /etc/nginx/snippets/ssl-wild.conf
```

```
ssl_certificate /etc/letsencrypt/live/kvlpro.ru/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/kvlpro.ru/privkey.pem;
ssl_trusted_certificate /etc/letsencrypt/live/kvlpro.ru/chain.pem;
```

*и прописываем его в конфигурационном файле сайта в блоке server*

```
# SSL
include ./snippets/ssl.conf;
include ./snippets/ssl-wild.conf;
```

![[Снимок экрана от 2026-04-14 13-28-29.png]]

***Создать snippet (конфигурационный файл отвечающий за общие настройки ssl):***

```
sudo vim /etc/nginx/snippets/ssl.conf
```

```
# SSL nginxconfig.io
ssl_session_timeout 1d;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off;

# Diffie-Hellman parameter for DHE ciphersuites
ssl_dhparam /etc/pki/tls/dhparam.pem;

# Mozilla Intermediate configuration
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;

# OCSP Stapling
#ssl_stapling on; #on self-signed certificates not necessary
ssl_stapling_verify on;
resolver 172.16.65.69 valid=60s;
resolver_timeout 2s;

```

***Настроить задачу по обновлению сертификата (сертификатов) из каталога /etc/letsencrypt/live/:***

```
sudo crontab -e
```

+++ @daily certbot renew

***Назад:*** [[program/3. nginx/!------Содержание------!|Содержание]]
