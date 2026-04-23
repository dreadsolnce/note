Настройка сбора логов используя rsyslog

Клиент (тот кто отправляет логи)

Установить программу rsyslog

```
sudo apt install rsyslog
```

Создать конфигурационный файл:

```
sudo vim /etc/rsyslog.d/elk.conf
```

```
# Загрузка модулей
module(load="imfile") # Загружаем модуль для чтения файлов. 

# Настройка чтения лог-файла и отправки данных по HTTP
# $InputFileName /var/log/nginx/back.border-patrol.ru_access.log
# $InputFileTag elk
# $InputRunFileMonitor

# Настройка в новом формате записи чтобы избежать предупреждения при проверке конфига коммандай sudo rsyslogd -N1
# rsyslogd: parameter "maxLinesAtOnce" not permited in inotify mode - ignored [v8.2302.0 try https://www.rsyslog.com/e/2222 ]

input(type="imfile"
    File="/var/log/nginx/back.border-patrol.ru_access.log"
    Tag="elk"
)

template (name="ForwardFormat" type="string" string="%timegenerated% %HOSTNAME% %syslogtag% %msg%\\n")

# Отправка логов по HTTP на 172.16.0.1 в формате CEF
if $programname == 'elk' then {
    action(
	type="omfwd"
	port="10514"
	protocol="udp"
	target="172.16.65.135" 
	template="ForwardForm```