# Установка:

- Перейти по ссылке ниже и сохранить текст в файл script.sh
[Ссылка на установочный скрипт](https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh)
- ```sudo chmod +x script.sh```
- ```curl --silent https://packages.gitlab.com/gpg.key | sudo apt-key add```
- ```sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 3F01618A51312F3F```
- ```sudo os=debian dist=bookworm ./script.sh```
- **В файле /etc/apt/sources.list.d/runner_gitlab-runner.list изменить путь до ключа:
![[Снимок экрана от 2025-04-25 16-43-32.png]]
- ```sudo apt update```
- ```sudo apt install gitlab-runner -y```
