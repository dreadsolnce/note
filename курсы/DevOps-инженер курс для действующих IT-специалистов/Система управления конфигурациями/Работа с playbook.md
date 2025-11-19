Проверка playbook-а без применения изменений:

`ansible-playbook -i inventory/prod.yml site.yml --check`

Старт (начало) выполнения playbook-а с определенной точки:

`ansible-playbook -I inventory/prod.yml site.yml --start-at-task`

Запуск playbook-а в интерактивном режиме (будет спрашивать подтверждение выполнения каждой таски):

`ansible-playbook -i inventory/prod.yml site --step`

---
Запуск playbook-а  с выводом дополнительной диагностической информаций (всего 6 уровней {букв v}):

`ansible-playbook -i inventory/prod.yml site.yml -v`

---
__Запуск playbook-а с запросом пароля sudo:__

```
ansible-playbook -i inventory/prod.yml zabbix.yml -K --diff --check
```

---
__Запуск playbook-а с выводом какие изменения в файлах происходят (аналог diff в github-е). Полезно использовать с ключом check:___

```bash
ansible-playbook -i inventory/prod.yml site.yml --diff --check
```

___
**Запуск playbook-а для определенной группы:**

```
ansible-playbook -l dev -i inventory/prod.yml site.yml --diff --check
```

***
*Запуск playbook-a по тегу:*

`ansible-playbook -i inventory/prod.yml site.yml --tags "tag1,tag2,tag3" `

*Запуск playbook-а с определенного play:*

`ansible-playbook your_playbook.yml --start-at-play "First Play"`
>>>>>>> Stashed changes
