Проверка playbook-а без применения изменений:

`ansible-playbook -i inventory/prod.yml site.yml --check`

Старт (начало) выполнения playbook-а с определенной точки:

`ansible-playbook -I inventory/prod.yml site.yml --start-at-task`

Запуск playbook-а в интерактивном режиме (будет спрашивать подтверждение выполнения каждой таски):

`ansible-playbook -i inventory/prod.yml site --step`

Запуск playbook-а  с выводом дополнительной диагностической информаций (всего 6 уровней {букв v}):

`ansible-playbook -i inventory/prod.yml site.yml -v`

