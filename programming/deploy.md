#### Деплоим свой React-сайт, или что делать на сервере

Когда ваш первый сайт готов, и VPS арендован, появляются закономерные вопросы: как развернуть проект на домене? Пост подойдет новичкам, которые впервые привязывают что-то свое к домену.


**Шаг 1. Стягиваем код**

Сначала клонируем репозиторий проекта на VPS:


git clone https://github.com/username/repo-name.git
cd repo-name/


Если для запуска понадобится Node.js определенной версии, установим через официальный скрипт:


curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs


После этого установим зависимости проекта (их указываем в package.json):


npm install


Если на сервере маловато оперативной памяти (например, минималка на 1-2 ГБ RAM) — может помочь добавление swap-файла:


sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile


**Шаг 2. Настраиваем переменные окружения**

Создаем файл .env в корне проекта. Админ использует условно бесплатную БД Supabase, потому шаблон состоит из таких средовых переменных:


NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=


Обратите внимание на префикс NEXT_PUBLIC_ — он необходим, чтобы переменные стали доступны на клиенте в приложениях, построенных на Next.js. 


**Шаг 3. Собираем проект**

Для деплоя нужна готовая сборка с оптимизированными файлами в папке build/


npm run build

**Шаг 4. Автоматический запуск с PM2**

pm2 — удобный менеджер процессов для Node.js, с его помощью каждый новый коммит будет «усвоен» автоматически. Устанавливаем глобально:

sudo apt install -y curl
sudo npm install -g pm2

Запускаем приложение через конфигурационный файл:

pm2 start ecosystem.config.js
pm2 save
pm2 startup

Последняя команда создаст автозапуск после перезагрузки сервера.

**Шаг 5. Установка и настройка Nginx**

nginx будет выступать обратным прокси и обеспечит работу по доменному имени. Устанавливаем:


sudo apt update
sudo apt install -y nginx

Создаём конфигурацию сайта:

sudo vim /etc/nginx/sites-available/helenkapatsa

Это стартовая версия конфиги. Адаптировать ее под свои нужды помогут документация и нейронки:

server {
    listen 80;
    server_name example.com www.example.com;

    location / {
        proxy_pass http://localhost:3000; # порт вашего React-приложения или PM2 процесса
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}


Активируем сайт и проверяем конфигурацию:


sudo ln -s /etc/nginx/sites-available/domain/etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

**Шаг 6. Получаем SSL-сертификат Let's Encrypt**

Для безопасной работы сайта нужен сертификат. Установим certbot:


sudo apt install -y certbot python3-certbot-nginx


Добавим в доменном регистраторе (например, reg.ru) ресурсные записи:
A - @ - [IP-адрес сервера без портов]
А - www - [IP-адрес сервера без портов]

«Усвоение» записей обычно занимает не больше получаса.

Запустим получение сертификата:


sudo certbot --nginx -d example.com -d www.example.com


Certbot автоматически настроит Nginx на использование HTTPS. Его, правда, придется перегенерировать раз в 4 месяца. Но можно настроить и автообновление.

Бывает, что, несмотря на отсутствие правил файервола, блокирующих порты 80 и 443, генерация SSL-сертификата через Let's Encrypt падает. Это может быть связано с политиками хостинг-провайдера, NAT, или другими непрозрачными ограничениями.

Если столкнулись с такой проблемой, попробуйте сменить хостинг-провайдера — чаще всего это решает вопрос с выдачей сертификата.

Это последний шаг, теперь ваш сайт на домене, и у вас в копилке новая ачивка :)

#### Деплоим с помощью docker

1. Создание ключей для подключения к docker контейнерам на удалённом сервере

[ссылка на ресурс](https://docs.docker.com/engine/security/protect-access/)
[ссылка на youtube](https://www.youtube.com/watch?v=XcpqfN8-2R0&list=PL_jzYoo7u8dFrlUGi7G-I8JO15m42JCnN&index=3)

sudo mkdir -p /etc/docker/tls

cd /etc/docker/tls

2. Генерируем CA key (корневой сертификат которым будет подписываться все остальное)

> [!NOTE] Описание CA key
> CA Key, или ключ центра сертификации (Certificate Authority), это секретный криптографический ключ, 
> который используется центром сертификации для подписи цифровых сертификатов. 
> Этот ключ необходим для подтверждения подлинности сертификатов и является основой для установления 
> доверия между сервером и клиентом при использовании https-соединений


> [!NOTE] Описание расширения PEM
> PEM, или Privacy Enhanced Mail, – это формат файлов, который широко используется для хранения и 
> передачи криптографических ключей и сертификатов, в основном в контексте безопасности электронной 
> почты и защиты соединений, таких как SSL/TLS
> Файл PEM может содержать различные типы данных, включая сертификаты (например, сертификаты SSL/TLS), 
> закрытые ключи и цепочки сертификатов

Приватный ключ:
sudo openssl genrsa -aes256 -out ca-key.pem 4096

	-> ввести секретную фразу (запомнить)

Публичный ключ:
sudo openssl req -new -x509 -days 365 -key ca-key.pem -sha256 -out ca.pem

	-> ввеси секретную фразу 

![[Снимок экрана от 2025-07-01 14-24-53.png]]

3. Генерируем серверный ключ:

sudo openssl genrsa -out server-key.pem 4096

Создаём запрос на подпись сертификата (csr):

> [!NOTE] Описание CSR
> Это текстовый файл, содержащий информацию о домене и организации, которая запрашивает сертификат, а также открытый ключ. 
> Генерация CSR необходима для получения SSL-сертификата, который обеспечивает безопасное соединение между сервером и клиентом

sudo openssl req -subj "/CN=**www.prod.kvlpro.ru**" -sha256 -new -key server-key.pem -out server.csr

4. Далее мы подпишем открытый ключ с помощью нашего центра сертификации:

Поскольку TLS-подключения могут быть выполнены как через IP-адрес, так и через DNS-имя, IP-адреса необходимо указать при создании сертификата:

echo subjectAltName = DNS:www.prod.kvlpro.ru,IP:212.23.211.120,IP:127.0.0.1 | sudo tee extfile.cnf

Установить расширенные атрибуты использования ключа демона Docker, которые будут использоваться только для аутентификации сервера:

echo extendedKeyUsage = serverAuth | sudo tee -a extfile.cnf

Теперь с генерируем подписанный сертификат:

sudo openssl x509 -req -days 365 -sha256 -in server.csr -CA ca.pem -CAkey ca-key.pem \
  -CAcreateserial -out server-cert.pem -extfile extfile.cnf

![[Снимок экрана от 2025-07-01 14-58-25.png]]

5. Для аутентификации клиента создайте клиентский ключ и запрос на подпись сертификата:
**Данный ключ пойдет на gitlab**

sudo openssl genrsa -out key.pem 4096

sudo openssl req -subj '/CN=client' -new -key key.pem -out client.csr

echo extendedKeyUsage = clientAuth | sudo tee extfile-client.cnf

Генерируем сертификат:

sudo openssl x509 -req -days 365 -sha256 -in client.csr -CA ca.pem -CAkey ca-key.pem \
  -CAcreateserial -out cert.pem -extfile extfile-client.cnf

6. Удаляем лишнее:

sudo rm -v client.csr server.csr extfile.cnf extfile-client.cnf

7. Изменяем разрешения на файлы:

sudo chmod -v 0400 ca-key.pem key.pem server-key.pem

sudo chmod -v 0444 ca.pem server-cert.pem cert.pem

8. Редактируем  запуск службы docker:

sudo systemctl edit docker

	[Service]
	ExecStart=
	ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --tlsverify --tlscacert=/etc/docker/tls/ca.pem --tlscert=/etc/docker/tls/server-cert.pem --tlskey=/etc/docker/tls/server-key.pem -H=0.0.0.0:2376

![[Снимок экрана от 2025-07-01 15-49-57.png]]

sudo systemctl daemon-reload

sudo systemctl restart docker.service

sudo systemctl status docker.service 

ps aux | grep docker | grep -v color

9. Для проверки подключения к докеру с  удалённой машины:
На удалённой машине должен быть установлен docker

Скопировать содержимое файла ca.pem с сервера

cat ca.pem

На локальном хосте создать каталог:

sudo mkdir /etc/docker/cert

cd /etc/docker/cert

sudo vim ca.prod.kvlpro.ru.pem <- вставить из буфера

cat cert.pem

sudo vim cert.prod.kvlpro.ru.pem <- вставить из буфера

cat key.pem

sudo vim key.prod.kvlpro.ru.pem <- вставить из буфера

10. Создаем подключение через docker:

docker context create prod --docker "host=tcp://www.prod.kvlpro.ru:2376,ca=/etc/docker/cert/ca.prod.kvlpro.ru.pem,cert=/etc/docker/cert/cert.prod.kvlpro.ru.pem,key=/etc/docker/cert/key.prod.kvlpro.ru.pem"

![[Снимок экрана от 2025-07-01 16-31-39.png]]

docker context ls

docker context use prod

11. На сервере открыть порт:

sudo ufw allow 2376/tcp

На локальной машине запустить какой нить контейнер

docker run -d -it --rm --name web-prod -p 80:80 nginx:latest

docker ps 

![[Снимок экрана от 2025-07-01 16-46-10.png]]

curl http://www.prod.kvlpro.ru -I -v
curl http://www.prod.kvlpro.ru -I
http://www.prod.kvlpro.ru

12. Создаём пустой проект на gitlab-е

![[Снимок экрана от 2025-07-01 16-53-21.png]]

mkdir infra
cd infra/
git init --initial-branch=main
git remote add origin git@gitlab.com:dreadsolnce-group/infra.git

