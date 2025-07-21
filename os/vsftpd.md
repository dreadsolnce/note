[Ссылка на ресурс](https://ru.wikihow.com/%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%B8%D1%82%D1%8C-%D0%B8-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B8%D1%82%D1%8C-FTP-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80-%D0%BD%D0%B0-Ubuntu-Linux)

[Новая ссылка на ресурс](https://losst.pro/ustanovka-ftp-na-ubuntu-16-04)

1. Устанавливаем сам пакет
```
sudo apt install vsftpd
```

2. Редактируем файл конфигурации сервера
```
sudo vim /etc/vsftpd.conf
```

local_enable=YES            # разрешить локальным пользователям входить в систему
write_enable=YES            # включить любую форму команды записи по FTP
ascii_upload_enable=YES     # отключить декорирование символов
ascii_download_enable=YES   # декорирование символов является плохой функцией

user_sub_token=$USER        # chroot может быть очень опасным. При использовании chroot 
chroot_local_user=YES       # убедитесь, что у пользователя нет прав на запись в каталог 
chroot_list_enable=YES      # верхнего уровня в # chroot

chroot_list_file=/etc/vsftpd.chroot_list 
local_root=/home/$USER/Public_html
allow_writeable_chroot=YES

ls_recurse_enable=YES

![[Снимок экрана от 2025-03-28 14-19-29.png]]


> [!NOTE] Замечание
> Если мы включаем опцию listen = YES, то обязательно отключить опцию listen_ipv6=YES


3. Добавить имена системных пользователей, которым будет разрешён доступ
```
sudo vim /etc/vsftpd.chroot_list
```

> [!NOTE] Пример файла
> kvl
> user

![[Снимок экрана от 2025-03-28 14-20-23.png]]
