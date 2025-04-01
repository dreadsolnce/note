### Добавление диска в lvm

- sudo fdisk /dev/sdb
	- p
	- n
	- p
	- enter; enter; enter; enter
	- t
	- L
	- 8e
	- w
- sudo pvdisplay
- sudo pvcreate /dev/sdb1
- sudo pvdisplay
- sudo lvdisplay
- sudo vgs
- sudo vgextend ubuntu-vg /dev/sdb1 (ubuntu-vg : имя volume group)
- sudo lvextend -l+100%FREE /dev/ubuntu-vg/ubuntu-lv (ubuntu-lv : имя logical volume)
- sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
- df -h

### Увеличение размера раздела

- sudo apt install parted
- sudo parted /dev/sda print free
- sudo pvs
- sudo vgs
- sudo lvs
![[Screenshot 2025-03-21 115018 1.png]]
- sudo lvextended -l +100%FREE /dev/VG736/lv_root
![[Screenshot 2025-03-21 115143.png]]
- df -h /root/
- sudo lvs
- sudo resize2fs /dev/VG736/lv_root
![[Screenshot 2025-03-21 115621.png]]
- df -h; df -h /root/
![[Screenshot 2025-03-21 115712.png]]

### Создание нового раздела из нового диска

- Создаем раздел на диске
	- sudo fdisk /dev/sdb
		- p
		- n
		- enter; enter; entert
		- t
		- 43
		- w
- Инициализируем раздел диска
	- pvcreate /dev/sdb1
- Создаём новую группу
	- vgcreate VGSTORAGE /dev/sdb1
- Создаём логический том
	- lvcreate -l 100%FREE -n lv_storage VGSTORAGE
- Создаём файловую систему
	- mkfs.ext4 /dev/VGSTORAGE/lv_storage

### Удаление lvm раздела

- Просмотр информации о логических томах
	- lvdisplay
- Удалить логический том
	- sudo lvremove /dev/VGSTORAGE/lv_storage
- Посмотреть информацию о группах томов
	- vgdisplay
- Удалить группу томов
	- sudo vgremove /dev/VGSTORAGE
