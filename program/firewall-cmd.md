## Открываем порт для внешних соединений всем пользователям

firewall-cmd --state
firewall-cmd --list-all
firewall-cmd --add-port=21/tcp
firewall-cmd --remove-port=21/tcp #эту команду используйте, чтобы закрыть порт

[Ссылка](https://www.dmosk.ru/miniinstruktions.php?mini=firewalld-centos)