 [Установка и настройка selectel](https://selectel.ru/blog/tutorials/how-to-install-and-use-postgresql-on-ubuntu-20-04/)

<h1>Установка:</h1>
```
sudo apt install postgresql postgresql-contrib
```

> [!NOTE] Информация
> <b>postgresql-contrib</b> - дополнительный модуль.
> Если вы используете готовую собранную версию PostgreSQL, доп. модули обычно поставляются в виде отдельного подпакета, например `postgresql-contrib`.

<h1>Переключение на пользователя администратора базы данных</h1>
```
sudo -u postgres -i
```

> [!NOTE] Информация
> Это суперпользователь для сервера баз данных PostgreSQL. По умолчанию у этого пользователя нет пароля и нет необходимости его устанавливать, <br>так как для переключения на пользователя postgres можно использовать sudo.


> [!warning] Ошибка: Доступ запрещен
> Если при возникновении ошибки в ОС AstraLinux 1.8
> **sudo: PAM account management error: Доступ запрещен**
> **sudo: a password is requiredontents**
> Данная ошибка возникает из-за того, что у администратора установлен **Уровень целостности=63(Высокий)**,
> а у пользователя postgres по умолчанию **Уровень целостности=0(Высокий)**
> ***==Решение:==***
> 	- войти в систему от имени администратора с **Уровнем целостности=0(Низкий)**,
> 	- войти в систему от имени пользователя **postgres**
> 	- установить уровень целостности=63(Высокий) для пользователя postgres
> 	<hr>
> 		<h6>sudo pdpl-user -i 63 postgres</h6>
> 	<hr>
> 

<h1>Создание пользователя для базы данных</h1>
```
sudo -u postgres -i
psql
CREATE USER dbtest WITH PASSWORD '12345678';
\q
exit
```

![[Снимок экрана от 2025-04-24 11-57-24.png]]

<h1> Создание базы данных</h1>
```
sudo -u postgres -i
createdb -E UTF8 -O dbtest dbtest
psql
\l
\q
exit
```
![[Снимок экрана от 2025-04-24 12-08-24.png]]

> [!NOTE] Информация
> 
> Название базы данных (<b>dbtest</b>), должно совпадать с именем пользователя базы данных (<b>dbtest</b>), <br>созданного в предыдущем шаге, иначе не сможем назначить права на эту базу данных своему пользователю.
> 

<h1>Назначение прав на базу пользователю</h1>
```
sudo -u postgres -i
psql
GRANT ALL PRIVILEGES ON DATABASE dbtest TO dbtest;
ALTER DATABASE dbtest OWNER TO dbtest;
\q
exit
```

![[Снимок экрана от 2025-04-24 12-16-13.png]]

<h1>Входим под учётной записью пользователя dbtest в базу данных dbtest:</h1>
```
exit
\conninfo
\q
```

![[Снимок экрана от 2025-04-24 12-20-36.png]]


> [!NOTE] Замечание:
> <p>Также можно подключиться к базе данных напрямую из ОС, но важно, <br>чтобы пользователь базы данных (dbtest) так же был создан в операционной системе.<br>В противном случае будет выдана ошибка!</p>

```
psql dbtest
```

![[Снимок экрана от 2025-04-24 12-27-18 1.png]]

<h1>Создание таблицы</h1>
```
psql -U dbtest -h 127.0.0.1 -p 5432
CREATE TABLE dbtable (id SERIAL PRIMARY KEY, name CHARACTER VARYING(255), phone CHARACTER VARYING(255), email CHARACTER VARYING(255));
\dt
exit
```

![[Снимок экрана от 2025-04-24 12-43-50.png]]

<h1><font color="red"> Импорт и экспорт базы данных</font></h1>
<h4>Сохранение базы данных</h4>
```
pg_dump -h localhost -U test test > test_$(date +%d-%m-%y).sql
```

<p><font color="green">Если база большая, то можно сразу делать ее в архив:</font></p>
```
pg_dump -h localhost -U test test > test_$(date +%d-%m-%y).tar.gzip 
```

> [!NOTE] Описание параметров
> <b>pg_dump</b>&emsp;&emsp;&emsp;&emsp;- вызов утилиты `pg_dump` для создания резервной копии базы данных;
> <b>-h localhost</b>&emsp;- адрес сервера на котором находится база данных
> <b>-U test</b>&emsp;&ensp;&ensp;&ensp;&ensp;&ensp;&thinsp;&thinsp;- имя пользователя, из-под которого производится подключение к БД;
> <b>test</b>&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;&thinsp;- имя базы данных, которая будет перенесена;

![[Снимок экрана от 2025-04-24 16-05-53.png]]
<h4>Экспорт базы данных</h4>
<ul>
<li>Создать роль базы (test) в случае её отсутствия</li>
<p><b>см. Создание пользователя для базы данных</b></p>
<li>Создаем пустую базу данных:</li>
<p><b>см. Создание базы данных</b></p>
<li>Назначаем права на базу:</li>
<p><b>см. Назначение прав на базу пользователю</b></p>
<li>Импортируем сохранённую базу данных</li>
<p><b>sudo -u postgres psql test < /tmp/test_24-04-25.sql </b></p>
</ul>
## Основные команды:

Команды терминала postgres:
	psql      : Подключиться к терминалу postgres
	\conninfo : Статус подключения
	\l        : Просмотр списка баз данных
	\du       : Просмотр пользователей
	\dt       : Просмотр таблиц
	\q        : Выход из терминала
	\с dbtest : Подключиться к базе dbtest
	\z public.file : Просмотреть разрешения на таблицу
	......................................................
	SELECT usename FROM pg_user;                  - просмотр пользователей
	SELECT \* FROM pg_user;                        - просмотр пользователей (более подробная информация)
	DROP TABLE dbtable;                           - удаление таблицы
	DROP DATABASE IF EXISTS < Имя базы данных >;  - удаление базы данных
	DROP USER IF EXISTS < Имя пользователя БД >;  - удаление пользователя базы данных
	SELECT * FROM dbtable;                        - просмотр содержимого таблицы 
	CREATE TABLE dbtable;                         - создание таблицы

***Просмотр списка баз данных, к которым имеет доступ пользователь:***
```
SELECT datname FROM pg_database WHERE has_database_privilege('имя_пользователя', datname, 'CONNECT');
```

***Проверка подключения к базе данных:***
```
psql -h 127.0.0.1 -p 5432 -U dbtest -d dbtest
```

***Задать пароль пользователю:***

```
sudo -u postgres -i
psql
ALTER USER <username> WITH PASSWORD '<password>';
```
