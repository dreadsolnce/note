1. ***Версия Enterprice Astra Linux 1.8***
___
**Установка расширения postgis**
- 1
```
sudo dpkg -i postgis-ent-17_3.4.4-1.18x8664_amd64.deb
```
- 2
```
sudo apt-get install -f
```
- 3
```
sudo dpkg -i postgis-ent-17_3.4.4-1.18x8664_amd64.deb
```
___
**Проверка установленной версии расширения**
- 1
```
sudo -u postgres -i
```
- 2
```
psql
```
- 3 : версия postgis
```
SELECT * FROM pg_available_extensions WHERE name = 'postgis';
```
- 4 : версия postgres
```
SELECT version();
```
___
