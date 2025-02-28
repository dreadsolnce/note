```
sudo -u postgres -i
```
> Переключение на пользователя postgres. Это суперпользователь для сервера баз данных PostgreSQL. По умолчанию у этого пользователя нет пароля и нет необходимости его устанавливать, так как для переключения на пользователя postgres можно использовать sudo

```
createuser _renderd
```
> Создание пользователя базы данных

```
createdb -E UTF8 -O _renderd gis
```
> Далее создаем базу данных с именем **gis** и одновременно с эти делаем osm владельцем базы данных. **-E UTF8** определяет схему кодировки символов, которая будет использоваться в базе данных.

```
psql
```
> Подключиться к терминалу postgres

```
\l

SELECT usename FROM pg_user;

SELECT * FROM pg_user;
```
> Посмотреть пользователей

```
\c gis
```
> Подключиться к базе данных

```
SELECT datname FROM pg_database WHERE has_database_privilege('имя_пользователя', datname, 'CONNECT');
```
> Просмотр списка баз данных, к которым имеет доступ пользователь

```
sudo -u postgres -i
psql
ALTER USER <username> WITH PASSWORD '<password>';
```
> Задать пароль пользователю

