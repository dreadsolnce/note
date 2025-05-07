## 1. Установка программы

### 1a. Установка на систему
1. sudo apt install -y curl openssh-server ca-certificates
2. curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
3. sudo GITLAB_ROOT_PASSWORD="password" EXTERNAL_URL="http://gitlab.test.local" apt-get install gitlab-ce
4. Сконфигурировать (опционально):
     sudo vim /etc/gitlab/gitlab.rb
        изменить external_url - если поставить https и у вас есть доменная запись, то сертификаты установятся автоматом.
     sudo gitlab-ctl reconfigure
    Посмотреть пароль:
        sudo cat /etc/gitlab/initial_root_password

### 1b. docker (не заработал)

1. Установить docker и docker-compose
2. Создать файл docker-compose.yaml
```
#version:3 
services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab
    restart: always
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://localhost:8080'
    ports:
      - "8080:80"
      - "8443:443"
      - "2222:22"
    volumes:
      - ./gitlab/config:/etc/gitlab
      - ./gitlab/logs:/var/log/gitlab
      - ./gitlab/data:/var/opt/gitlab
```
3. Запустить контейнер
`docker compose up -d`

4. Перейти по url: http://localhost:8080
5. При первом входе установить пароль пользователя root

## 2. Установка и регистрация GitLab Runner

### Установка ранера
1. curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
2. sudo apt install gitlab-runner -y
3. Проверить что gitlab runner запущен: ```sudo gitlab-runner status```
![[Снимок экрана от 2025-04-25 16-46-19.png]]

### 2. Создание и запуск ранеров

<h1>Пример регистрации ранера для нового проекта </h1>
<ul>
<li><b>Открыть проект</b></li>
<img src="Снимок экрана от 2025-04-24 18-19-43.png">
<li><b>Перейти в Settings -> CI/CD -> Runners -> New project runner</b></li>
<img src="Снимок экрана от 2025-04-24 18-25-58.png">
<li><b>В поле Tags указать тэг: например название удалённого сервера</b></li>
<img src="Снимок экрана от 2025-04-25 17-55-12.png"> 
<!--<li><b>Отметить чекбокс [Run untagged jobs]</b></li>
<img src="Снимок экрана от 2025-04-25 14-59-41.png"> -->
<li><b>В поле Runner description понятное описание</b></li>
<img src="Снимок экрана от 2025-04-25 17-56-41.png">
<li><b>Отметить пункт Lock...</b></li>
<p><b><font color="orange">(ранер будет доступен только для этого проекта)</font></b></p>
<!--<p class="test">test</p>-->
<img src="Снимок экрана от 2025-04-25 10-48-43.png">
<li><b>Create runner!</b></li>
</ul>

<ul>
<li><b>Скопировать команду из Step1 и вставить в терминал на сервере</b></li>
<img src="Снимок экрана от 2025-04-25 11-20-59.png">
<li><b>Открываем терминал на сервере и запускаем комманду</b></li>
<img src="Снимок экрана от 2025-04-25 11-48-48.png">
<li><b>Переходим обратно на сайт gitlab и нажимаем [View runners]</b></li>
<img src="Снимок экрана от 2025-04-25 11-51-27.png">
<li><b>В результате у нас будет создан (зарегистрирован) ранер, который будет отображен в списке.</b></li>
<p class=test><b>Данный ранер будет запускаться (выполнять) действия (джобы, команды) на сервере</b></p>
<img src="Снимок экрана от 2025-04-25 14-19-34.png">
<li><b>Данный ранер добавился, но он еще не функционирет!</b></li>
<p class=text1><b>На сервере (в терминале) необходимо выполнить команду: gitlab-runner run </b></p>
<img src="Снимок экрана от 2025-04-25 14-18-36.png">
<img src="Снимок экрана от 2025-04-25 14-25-36.png">
<li><b>В результате наш ранер успешно будет загружен!</b></li>
<img src="Снимок экрана от 2025-04-25 14-25-48.png">
</ul>

> [!warning] Важное замечание!
> Локальный файл со всеми ранерами, которые были сделаны на сервере находятся
> в папке /home/текущий пользователь/.gitlab-runner/config.toml, а настройки
> системного ранера находятся в файле /etc/gitlab-runner/config.toml
> 
> Запускать runner необходимо из **домашней директории текущего пользователя!**
> 


> [!NOTE] Еще одно важное замечание
> Команда запуска ранера в фоновом режиме:
> ```nohup gitlab-runner run >/dev/null 2>&1 &```
> где nohup - что бы не закрывался процесс после закрытия удалённой сессии
> а также перенаправление всех сообщений в пустоту, что бы не забивался
> файл nohup.out, куда по умолчанию скидывается вся инфа о работе команды nohup
## 3. Настройка деплоя на сервере

> [!warning] Важно
> **Перед настройкой деплоя на сервере, необходимо чтобы <br>репозиторий с проектом был залит на сервере gitlab**

### 1. Настройка службы gitlab-runner.service 
<div class="text2">Настройка данной службы необходима для авто запуска программы gitlab-runner</div>

<div> После успешного локального запуска командой <b>gitlab-runner run</b>, <br>необходимо скопировать настройки в конфигурационный файл службы <b>gitlab-runner.service</b>,<br></div>

```
sudo cp /etc/gitlab-runner/config.toml /etc/gitlab-runner/config.toml.orig
sudo cp /home/*текущий пользователь*/.gitlab-runner/config.toml /etc/gitlab-runner/config.toml
sudo systemctl restart gitlab-runner.service
sudo systemctl status gitlab-runner.service
```

*Настроить пользователя gitlab-runner для возможности запуска команды /usr/bin/git через sudo:*

```
sudo pdpl-user -i 63 gitlab-runner
```

![[Снимок экрана от 2025-05-06 14-33-42.png]]
### 2. Настройка токена для деплоя
> [!warning] Важно
> *Для того чтобы с сервера gitlab забирался проект, необходимо настроить доступ к нему используя специальный токен для deploy*
 
**На сервере gitlab:**
- Перейти в папку проекта
- Settings -> Repository -> Deploy tokens -> Add token
![[Снимок экрана от 2025-05-06 12-37-41.png]]
![[Снимок экрана от 2025-05-06 12-40-08.png]]
![[Снимок экрана от 2025-05-06 12-40-14.png]]
- Заполнить регистрационные поля:
	- В поле name ввести имя токена (можно по названию сервера)
	- В поле username ввести имя (можно также по названию сервера)
	- Отметить чек бокс read_repository
- Нажать "Create deploy token"
![[Снимок экрана от 2025-05-06 12-44-18.png]]
- Необходимо сохранить пароль, т.к. после закрытия окна информацию о пароле будет невозможно увидеть
![[Снимок экрана от 2025-05-06 12-45-55-1.png]]
### 3. Настройка сервера для клонирования в него репозитория

1. Т.к. служба gitlab-runner.service запускается от имени пользователя gitlab-runner,
то необходимо пользователю gitlab-runner разрешить выполнение команды git через sudo 
без ввода пароля:
```
sudo visudo
```

 Добавить следующую строку:

`gitlab-runner ALL=NOPASSWD: /usr/bin/git`

![[Снимок экрана от 2025-05-06 12-55-14.png]]

### 4. Клонирование репозитория с сервера gitlab:

1. Перейти в каталог html:
```
cd /var/www/html/

```

2. С клонировать репозиторий используя учетный данные токена:
```shell
sudo git clone https://<username>:<deploy_token>@gitlab.example.com/tanuki/awesome_project.git
```
![[Снимок экрана от 2025-05-06 13-02-28-1.png]]

![[Снимок экрана от 2025-05-06 13-01-33.png]]
![[Снимок экрана от 2025-05-06 13-01-57.png]]
![[Снимок экрана от 2025-05-06 13-02-28.png]]
![[Снимок экрана от 2025-05-06 13-02-46.png]]
![[Снимок экрана от 2025-05-06 13-03-08.png]]

## 4. Примеры тестовых ранеров!

### Пример №1
<h3>Пример тестового ранера!</h3>

> [!NOTE] Описание
> Данный тестовый ранер будет выполнять команды (джобы) на сервере!
> При push изменений на сервер, ранер запустит команду echo 

- **Создаем в корне проекта файл .gitlab-ci.yml со следующим содержимым
![[Снимок экрана от 2025-04-25 12-11-23.png]]
- **Пушим изменения на сервер
- **Переходим на сайт gitlab
- **Открываем наш проект
- **Build -> Pipelines
![[Снимок экрана от 2025-04-25 12-16-34.png]]
![[Снимок экрана от 2025-04-25 14-36-56.png]]
- **Результат выполнения ранера
![[Снимок экрана от 2025-04-25 14-37-16.png]]

### Пример №2
![[Снимок экрана от 2025-05-06 14-27-00.png]]