***Предоставьте разрешения текущей учетной записи на каталог osm.***
```
sudo apt install acl
```

```
cd /home/osm
```

```
# Предоставьте разрешения текущей учетной записи на каталог osm.
sudo setfacl -R -m u:<test_user>:rwx /home/osm/
```

```
# Удалить разрешения учётной записи gitlab-runner на каталог skillbox
sudo setfacl -x user:gitlab-runner /var/www/html/skillbox/
```

