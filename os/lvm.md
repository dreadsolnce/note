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