##### 1. Настройка репозитория:

> [!NOTE] Title
> **В случае появления следующих ошибок:**
> *Errors during downloading metadata for repository 'baseos':
> Errors during downloading metadata for repository 'appstream':
> Errors during downloading metadata for repository 'crb':*
 

```
sudo vim /etc/yum.repos.d/centos.repo
```

Закоментировать ключ `baseurl` и раскоментировать ключ `metalink`

![[Снимок экрана от 2025-10-10 14-58-30.png]]
![[Снимок экрана от 2025-10-10 14-58-42.png]]
![[Снимок экрана от 2025-10-10 14-58-51.png]]
