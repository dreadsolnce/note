******
==***через pecl (расширение msgpack)***== ^e23c2e

1. Установка системных зависимостей

Для сборки расширения вам понадобятся инструменты разработки PHP.
  ```bash
  sudo apt-get install php-dev php-pear build-essential
  ```

2. Установка через PECL

Выполните команду установки:
```bash
sudo pecl install msgpack
```

3. Включение расширения в PHP
Создать файл с названием расширения:
```bash
sudo vim /etc/php/8.4/mods-available/msgpack.ini
```

```txt
extension=msgpack.so
```

4. Создать ссылки (включить) на расширение
	- для интерпретатора cli
		`sudo ln -s /etc/php/8.4/mods-available/msgpack.ini /etc/php/8.4/cli/conf.d/20-msgpack.ini`
	- для интерпретатора fpm
		`sudo ln -s /etc/php/8.4/mods-available/msgpack.ini /etc/php/8.4/fpm/conf.d/20-msgpack.ini`

5. Перезагрузить службу php-fpm
```
sudo systemctl reload php8.4-fpm.service
```
6. Проверка
	- для cli
		`php -m | grep msgpack`
		`php --ri msgpack`
	- для fpm
		`/usr/sbin/php-fpm8.4 -m | grep msgpack`


==***установка (обновление) librdkafka-dev для расширения php-rdkafka***== ^2df143

1. Устанавливаем зависимости для сборки
 
 ```
 sudo apt-get update
 ```

```
sudo apt-get install -y build-essential python3 cmake libssl-dev zlib1g-dev libzstd-dev
```
 
2. Скачиваем и собираем librdkafka (например, версию v2.3.0)

```
git clone https://github.com/confluentinc/librdkafka.git
```

```
cd librdkafka
```

```
git fetch --tags
```

```
git checkout v2.13.0
```

3. Конфигурация и сборка

```
./configure
```

```
make -j$(nproc)
```

```
sudo make install
```

ОБЯЗАТЕЛЬНО: обновляем кэш динамических библиотек, 
чтобы система увидела новую версию по старым путям
```
sudo ldconfig
```

4. Проверка
- для cli
	`php -m | grep rdkafka`
	`php --ri rdkafka`
- для fpm
	`/usr/sbin/php-fpm8.4 -m | grep rdkafka`


==***установка openswoole***== ^49b785


> [!NOTE] ВАЖНОЕ ЗАМЕЧАНИЕ
> php-openswoole не работает одновременно с установленным swoole.
> Поэтому перед установкой важно убедиться, что не установлен пакет php8.4-swoole

**Есть три способа установки пакета openswoole: 
- 1) через pecl 
- 2) через менеджер пакетов apt 
- 3) через компиляцию, пакеты для которого вкачиваются из репозитория github**
___
***2 способ: с помощью пакетного менеджера apt:***

```
sudo apt list --installed | grep swoole
```

удалить при необходимости пакет swoole

```
sudo apt install php8.4-openswoole
```

```
php --ri openswoole
```
___
==***установка pm2-logrotate***== ^b891df

**Есть два способа установки:**
- 1) не помню
- 2) глобальный с помощью npm
___
***2 способ: с помощью npm***

```
npm install -g pm2-logrotate
```

```
npm list -g pm2-logrotate
```

Запуск через pm2 (с настройками по умолчанию):
```
sudo -E -u user pm2 start /usr/local/lib/node_modules/pm2-logrotate/app.js --name pm2-logrotate
```

Запуск через pm2 (с пользовательскими настройками):
```
sudo -E -u user pm2 start /usr/local/lib/node_modules/pm2-logrotate/app.js --name pm2-logrotate --env '{"max_size": "10M", "retain": "7", "compress": "true", "rotateInterval": "0 0 * * *"}'
```

***Назад:*** [[program/2. php-fpm/!------Содержание------!|!------Содержание------!]]
  
  
