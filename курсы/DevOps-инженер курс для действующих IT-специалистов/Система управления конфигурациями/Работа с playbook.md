Проверка playbook-а без применения изменений:

`ansible-playbook -i inventory/prod.yml site.yml --check`

Старт (начало) выполнения playbook-а с определенной точки:

`ansible-playbook -I inventory/prod.yml site.yml --start-at-task`

