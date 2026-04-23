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

***Назад:*** [[program/2. php-fpm/!------Содержание------!|!------Содержание------!]]
  
  
