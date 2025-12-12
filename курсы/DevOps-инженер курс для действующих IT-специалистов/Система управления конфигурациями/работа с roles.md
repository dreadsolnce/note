
***Установка существующей роли***

1. В каталоге с playbook-ом создать файл requirements.
	
	 ![[Снимок экрана от 2025-09-25 14-38-00 2.png]]

1. Установить role в playbook

	1. `ansible-galaxy install -r requirements.yml -p roles`
	
	2. будет создана папка roles, в которой будет находится под папка clickhouse со скачанной ролью
	
	 ![[Снимок экрана от 2025-09-25 14-39-39 2.png]]
	
	 ![[Снимок экрана от 2025-09-25 14-39-58 2.png]]

***Создание своей роли:***

1. Инициализируем пустую роль:
		
		`ansible-galaxy role init vector-role`
		
2. Будет создана структура проекта по умолчанию для ansible роли

	![[Снимок экрана от 2025-09-25 14-45-20 2.png]]

3. Наполнить каталоги кодом 
4. Создать на github новый проект vrctor-role
5. Инициализировать проект git в локальной директории с ролью
6. `git iinit`
7. `git branch -M main`
8. `git status`
9. git add .
10. `git add remote origin git@github.com:dreadsolnce/vector-role.git`
11. `git commit -m "Initial commit"`
12. `git tag v.1.0.0`
13. `git log`
14. `git push origin main`
15. `git push origin v.1.0.0`


