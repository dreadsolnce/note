**Установка:**
`apt install mysql-server`

**Удаление:**
`sudo systemctl stop mysql
`sudo apt-get purge mysql-server mysql-client mysql-common mysql-server-core-* mysql-client-core-*
`sudo apt-get autoremove
`sudo apt-get autoclean
`sudo rm -rf /etc/mysql /var/lib/mysql
`sudo deluser mysql
`sudo delgroup mysql
`dpkg -l | grep mysql

**Подключение:**
`sudo -u root mysql

**Просмотр пользователей:**
SELECT host, user, password_last_changed, password_expired FROM mysql.user;

**Создание базы данных:**
`CREATE SCHEMA animals;
*где animals - имя базы данных*

**Просмотр баз данных:**
`SHOW DATABASES;

**Создание пользователя c паролем secret:**
`CREATE USER 'test'@'localhost' IDENTIFIED BY 'secret';
`FLUSH PRIVILEGES;

**Предоставление пользователю animals прав доступа на БД animals:**
`GRANT SELECT, INSERT, UPDATE, DELETE, ALTER ON animals.* TO 'animals'@'localhost';
                   или полные права
`GRANT ALL PRIVILEGES ON `animals`.* TO 'animals'@'localhost';
`FLUSH PRIVILEGES;

**Оптимизированный вывод:**
`SELECT host, user, password_expired FROM mysql.user\G;

**Задать пароль wordpress пользователю wordpress:**
ALTER USER 'wordpress'@'localhost' IDENTIFIED BY 'wordpress';

**Просмотр плагина валидации пароля:**
`SHOW GLOBAL VARIABLES LIKE 'validate_password%';
![[Снимок экрана от 2025-06-09 12-06-26.png]]

**Отключение политики (содержание хотя бы одного спец.символа в пароле):**
`SET GLOBAL validate_password.special_char_count = 0;
![[Снимок экрана от 2025-06-09 12-07-05.png]]
