[статья](https://jino.ru/spravka/articles/f2b.html#ustanovka)

sudo apt install fail2ban

sudo systemctl enable fail2ban

sudo vim /etc/fail2ban/jail.local

![[Screenshot 2025-06-02 165634.png]]

Этот набор условий означает, что IP-адрес, с которого было произведено 6 неудачных попыток SSH-подключения за последний час (3600 секунд), будет заблокирован на одни сутки (86400 секунд). Не будут блокироваться IP-адреса в диапазоне от 127.0.0.1 до 127.0.0.8 и адрес 23.34.45.56.

sudo systemctl start fail2ban

sudo fail2ban-client status sshd