#### Настройка синхронизации

### Шаг 0: Установить Obsidian на все свои устройства

На случай если его у вас ещё нет, вот ссылка:

- [https://obsidian.md/download](https://obsidian.md/download)
    

### Шаг 1: Создать приватный репозиторий Github

1. Залогиниться на [github](https://github.com/)
    
2. По ссылке [https://github.com/new](https://github.com/new) указать:
    
    - имя репозитория
        
    - выбрать: приватный
        
    - выбрать: файл README (т.к. репозиторий не д.б. пустым)
        
3. создать репозиторий (зеленая кнопка внизу)
    
4. в корне репозитория создать файл `.gitignore` с текстом `.obsidian`
    

![Настройки нового репозитория на GitHub](https://habrastorage.org/r/w1560/getpro/habr/upload_files/b8c/96b/9b0/b8c96b9b0cbfa41d775de64a11a0b8ac.png "Настройки нового репозитория на GitHub")

Настройки нового репозитория на GitHub

![Создание .gitignore (1/2)](https://habrastorage.org/r/w1560/getpro/habr/upload_files/abb/6fa/625/abb6fa6257d35910acfd35e6ab5fb677.png "Создание .gitignore (1/2)")

Создание .gitignore (1/2)

![Создание .gitignore (2/2)](https://habrastorage.org/r/w1560/getpro/habr/upload_files/0b7/b44/386/0b7b4438653dbfdad2d90607404f99ca.png "Создание .gitignore (2/2)")

Создание .gitignore (2/2)

> Комментарий: файл `.gitignore` нужен, чтобы не синхронизировать папку `.obsidian`, в которой находятся локальные настройки (конфликтуют на разных устройствах при синхронизации). Если этого не сделать — вся схема перестанет работать.

### Шаг 2: Создать токен доступа

1. По ссылке [https://github.com/settings/tokens/new](https://github.com/settings/tokens/new) указать:
    
    - имя токена
        
    - выбрать: No Expiration (значит токен не устареет)
        
    - выбрать repo
        
2. создать токен (зеленая кнопка)
    
3. сохранить созданный токен
    
    - GitHub покажет токен вида `ghp_1wKdGc4IhVCHxXEaKClMNp1rEMwsHu1f5zNZ` только один раз, его нужно скопировать и сохранить. Если потеряется - придётся создавать новый.
        

![Настройки "простого" токена доступа](https://habrastorage.org/r/w1560/getpro/habr/upload_files/bce/e82/ac9/bcee82ac94f9fde7344dfd29d058a98e.png "Настройки "простого" токена доступа")

Настройки "простого" токена доступа

Только для "параноиков"

1. [](https://github.com/settings/personal-access-tokens/new)
    

### Шаг 3: Синхронизация с десктопом (Windows, Linux, Mac)

Приведу пример для Windows.

#### 3а: клонировать репозиторий

1. Убедиться, что git установлен в системе
    
    - В консоли набрать `git --version` и увидеть версию.
        
    - Если вылезла ошибка:
        
        - **Установить** гит: [https://git-scm.com/downloads](https://git-scm.com/downloads)
            
        - **Выполнить первоначальную настройку** гит-а: в командной строке указать свои "email" и "имя" (любые), которыми будут подписываться ваши файлы в гите (у вас приватный репозиторий, значит их никто не увидит)
            
            ```
            git config --global user.email МОЙ-EMAILgit config --global user.name МОЁ-ИМЯ
            ```
            
            _Гит выполняет все операции "от имени" юзера, определённого этими двумя командами. Пока он не имеет этих данных, функционал гит-а недоступен._
            
2. Создать папку, в которой будут храниться «Vaults/Хранилище» Obsidian (Vault — это аналог Workspace в Notion). Например: `C:/OBSIDIAN` (win) или `~/OBSIDIAN`(linux).
    
3. Собрать https-ссылку, по которой будет синхронизироваться github
    
    - Формат ссылки: `https://<PERSONAL_ACCESS_TOKEN>@github.com/<USERNAME>/<REPO>.git`,
        
    - Например: `https://ghp_1wKdGc4IhVCHxXEaKClMNp1rEMwsHu1f5zNZ@github.com/myaccount/my-obsidian-vault.git`
        
4. Запустить консоль (`cmd.exe` в windows)
    
    - в консоли перейти в папку OBSIDIAN (`cd /OBSIDIAN`) и оттуда выполнить команду с собранной ссылкой `git clone https://<PERSONAL_ACCESS_TOKEN>@github.com/<USERNAME>/<REPO>.git`
        

> В результате появится папка `OBSIDIAN/my-obsidian-vault`, а в ней файлы из GitHub (`README.md` и `.gitignore`, а также папка `.git` с настройками гитхаба)

#### 3b: создать Хранилище/Vault в Obsidian на основе скаченной папки

![Создание нового Хранилища/Vault в Obsidian](https://habrastorage.org/r/w1560/getpro/habr/upload_files/290/ce2/bb4/290ce2bb4e061c7bafb682ab97363479.png "Создание нового Хранилища/Vault в Obsidian")

Создание нового Хранилища/Vault в Obsidian

1. Из программы Obsidian: открыть синхронизированную папку `my-obsidian-vault` как хранилище
    
2. Теперь можно заполнить "хранилище" заметками и файлами, или просто отредактировать `README.md`
    

> Если у вас уже есть заметки в Obsidian, то алгоритм будет тот же:
> 
> - сначала создать и синхронизировать пустой репозиторий с github
>     
> - затем скопировать туда все файлы из старого хранилища
>     

#### 3с: Настроить синхронизацию через obsidian-плагин 'Git'

![Плагин Git в Obsidian (1/4) - поиск и установка](https://habrastorage.org/r/w1560/getpro/habr/upload_files/707/9ae/22d/7079ae22da30a6e6ea824d7c8573c0ed.png "Плагин Git в Obsidian (1/4) - поиск и установка")

Плагин Git в Obsidian (1/4) - поиск и установка

> **UPDATE**: в новых версиях плагина Obsidian-git, термин "_Backup"_ (на скриншотах далее) уже заменили на термин на "_Commit-and-sync_"

1. В настройках Обсидиана, в разделе "Community plugins" найти и установить плагин 'git'
    
2. Далее, в настройках самого плагина git, установить:
    
    - **Vault Backup Interval (minutes)**: 1
        
    - **Auto Backup after stopping file edits**: ВКЛ  
        
3. **Pull updates on startup**: ВКЛ
    
4. **Disable notifications**: ВКЛ (можно не включать, но тогда каждую минуту будут появляться уведомления)
    

![Плагин Git в Obsidian (2/4) - настройка](https://habrastorage.org/r/w1560/getpro/habr/upload_files/d5f/b64/a79/d5fb64a7997df046c574c4b64e77e0dd.png "Плагин Git в Obsidian (2/4) - настройка")

Плагин Git в Obsidian (2/4) - настройка

![Плагин Git в Obsidian (3/4) - настройка](https://habrastorage.org/r/w1560/getpro/habr/upload_files/b1b/1ac/553/b1b1ac5537fafefef927521d99c88602.png "Плагин Git в Obsidian (3/4) - настройка")

Плагин Git в Obsidian (3/4) - настройка

![Плагин Git в Obsidian (4/4) - настройка](https://habrastorage.org/r/w1560/getpro/habr/upload_files/70a/1e7/7c6/70a1e77c6c97d83d8c41c4aa7f8f0f94.png "Плагин Git в Obsidian (4/4) - настройка")

Плагин Git в Obsidian (4/4) - настройка

Теперь все заметки из этого хранилища, каждую минуту после завершения редактирования, будут загружаться в Github. Также, при запуске Obsidian, последняя версия заметок сразу подгрузится с GitHub.

> Если не хочется ждать минуту, то принудительно загрузить изменения на GitHub можно через Obsidian-команду "Git: Create backup": `CTRL+P`, `git b`. Также (в настройках Obsidian) можно создать горячую клавишу для этой команды (например, `ALT+S`)

### Шаг 4: Синхронизация с мобильным (Android, iOS)

Здесь всё проще, чем на десктопе:

![Поиск и установка плагина Fit в мобильном](https://habrastorage.org/r/w1560/getpro/habr/upload_files/21f/a19/a47/21fa19a47db9990771dc32aef2d71d91.png "Поиск и установка плагина Fit в мобильном")

Поиск и установка плагина Fit в мобильном

1. Открыть приложение Obsidian
    
2. Создать новый **пустой** Vault/Хранилище (не создавать в нём файлы)
    
3. Внутри Obsidian найти и установить community-плагин "Fit"
    
4. В настройках плагина:
    
    - вставить Токен (из шага 1). Именно токен, не url.
        
    - авторизоваться по кнопке "**Authenticate user**"
        
    - **Github repository name**: выбрать из списка, например: `my-obsidian-vault`
        
    - **Branch name**: выбрать `main`. (тут без вариантов)
        
    - **Auto sunc**: выбрать `Muted`
        
    - **Auto check interval**: установить 1 минуту
        
    - **File change**: нажать, чтобы кнопка не горела (и не спамила каждую минуту).
        

Всё, настройка мобильного приложения завершена: Obsidian будет синхронизироваться с GitHub каждую минуту.

#### Недостатки плагина Fit версии v1.1.1

![Кнопка с котиком для принудительной синхронизации через плагин Fit](https://habrastorage.org/r/w1560/getpro/habr/upload_files/a81/c5f/37e/a81c5f37e38821a23c5302bc2c2155e6.png "Кнопка с котиком для принудительной синхронизации через плагин Fit")

Кнопка с котиком для принудительной синхронизации через плагин Fit

_К сожалению, плагин Fit (пока ещё) не умеет делать автосинхронизацию "при старте".  
  
Поэтому, сразу после запуска, чтобы не ждать минуту, можно подтянуть изменения через кнопку с котиком "Fit Sync"._

Также, в текущей версии плагина есть один "баг": если на десктопе удалить ненужную папку-с-файлами (и синхронизировать репозиторий), то на мобильном файлы удалятся, но пустая папка останется ... и её придётся удалить вручную. Неудобно, но такой сценарий случается довольно редко. Плюс есть шанс что автор плагина исправит этот баг.

При этом у плагина есть бесспорное достоинство: работает на мобильном.

### Шаг 5: техника безопасности

**Не редактируйте один и тот же файл одновременно на двух устройствах.**

> _Впрочем, этот совет относится и к Notion: я однажды терял часть текстов в Notion, когда заметка была открыта в нескольких местах_

На самом деле, следовать этому совету легко.

А если что-то пойдёт не так, то Obsidian сообщит о конфликте версий, и с ним придётся разбираться (в то время как Notion молча как-то всё решит сам)

- На десктопе конфликт версий разрешается средствами GIT-а.
    
    - Если кратко: то достаточно отредактировать "конфликтный" файл и удалить из него ненужные строки (в т.ч. служебные), чтобы всё пришло в норму.
        
    - а если одновременно редактировались _разные_ строки заметки, то умный git разрулит всё сам (корректно, незаметно, автоматически)
        
- На мобильном конфликт решается иначе: локальный файл не меняется, но его "внешняя" копия появляется в папке `_FIT`. Нужно смотреть глазами и решать головой, что оставить и что поменять в оригинальном файле.
    

Ещё раз: конфликт версий заметки — это форс-мажор, **скорее всего вы никогда с ним не встретитесь** (если не будете редактировать одну и ту же заметку одновременно на двух устройствах). А если встретитесь — его легко разрулить.

### Вопросы и ответы:

#### Почему на мобильных используется плагин Fit, а не полноценный Git?

Мобильные приложения не могут вызывать другие мобильные приложения, поэтому плагин Obsidian не может вызвать стороннее приложение Git на мобильном.

Плагины Obsidian написаны на JS. В мобильном плагине obsidian-git есть JS-реализация полноценного гит-а… но она ещё сырая (тормозит на большом количестве файлов и спамит алертами).

Поэтому для мобильных устройств приходится использовать не полноценный Git, а плагин Fit, который использует упрощённый GitHub API.

#### Почему на десктопе используется плагин Git, а не простой Fit?

Тут две причины:

- хранение всей истории изменений на локальной машине (в папке .git)
    
- удобство автоматического разруливания конфликта версий, если на разных устройствах одновременно редактировались разные строки одного файла.
    

#### Можно ли использовать только мобильную версию (без десктопа)

Да, вполне. Шаг 3 можно пропустить - всё будет работать.

Правда десктопная версия даёт дополнительную резервную копию с историей изменений (хранится на десктопе), но если отказаться от десктопа — система продолжить работу.

Также можно отказаться от мобильной версии и работать только на десктопе (но зачем?:)

#### Почему бы не синхронизировать всё через сторонние приложения (например, SyncThing)?

- они требуют внимания (надо следить, что ничего не отвалилось)
    
- они разряжают батарею (на мобильных)
    
- они гоняют больше трафика (на мобильных)
    
- синхронизация через них происходит чуть дольше
    
- они не хранят историю всех изменений (как это делает git в локальной папке `.git` и на github)
    
- самое главное — с ними возможен небезопасный сценарий: _случайно удалил на одном устройстве — синхронизировалось/удалилось сразу везде_. Чтобы этого избежать — следует как-то где-то дополнительно настраивать бекап.
    

Здесь же всё в одном приложении: запустил Obsidian — синхронизация идёт; закрыл — не идёт.

#### Где смотреть историю изменений файлов?

![История изменений файла на GitHub](https://habrastorage.org/r/w1560/getpro/habr/upload_files/571/348/0bb/5713480bb23b1e0685abc3cf861c5dd4.png "История изменений файла на GitHub")

История изменений файла на GitHub

Если вы не сильны в git (как и я), то историю изменений любого файла можно посмотреть на сайте github (для каждого файла справа-вверху есть кнопка "🕓History").

#### Что делать, если GitHub однажды перестанет открываться из РФ?

Действительно, плагин Fit жестко привязан к API GitHub-а.

Но тут как в той притче - "_или ишак подохнет, или падишах…_".

- Скорее всего к этому моменту допилят мобильный плагин Obsidian-Git. И тогда можно будет использовать любой git-сервер (даже свой собственный).
    
- Или допилят плагин Fit (или создадут новый) для работы с каким-нибудь опенсорс-сервисом.
    
- или действовать по-старинке:
    
    - Android: синхронизироваться через внешнее приложение "Termux", внутрь которого можно установить полноценный git (инструкция гуглится)
        
    - iOS: синхронизироваться через iCloud
        
- наконец, у Obsidian есть свой сервис синхронизации за $4 в месяц. Правда к нему хорошо-бы прикрутить дополнительный бекап.
    

> В январе я уже [предсказывал](https://habr.com/ru/articles/785186/) замедление youtube. Так что побуду предсказателем ещё раз. Так вот: гитхаб в ближайшие годы останется доступным (ну а вдруг:)

#### Что ещё можно сделать с этой схемой?

- В Obsidian можно использовать несколько Хранилищ/Vaults (аналог Workspace в Notion), и под каждое создать свой репозиторий на гитхаб. Правда синхронизацию придётся настраивать отдельно для каждого: в Obsidian плагины устанавливаются внутрь Vault, а не в приложение в общем.
    
- Если есть свой сервер — можно настроить ещё один бекап: резервное копирование репозитория GitHub по расписанию через git.
    

### Минутка здравого смысла

Как психолог по своей основной профессии, я смею утверждать, что способность **владеть** (обладать, распоряжаться, быть хозяином) — одно из базовых качеств, необходимых для хорошей жизни.

При чём здесь Obsidian?

Дело в том, что реализовываться (по своим ценностям) у человека получается только из состояния владения своей жизнью (когда ощущаешь право и способность влиять). А воспитывается способность владеть и влиять всегда с мелочей, в том числе через владение результатами своего творчества. В том числе через владение личными документами.

А современную ситуацию — где корпорации пытаются распоряжаться контентом авторов и фактически предлагают им отказаться от права владения — я рассматриваю лишь как временный перекос и "болезнь роста" нашей цивилизации.

При этом хочу разделить понятия "владение" и "управление": очень удобно, когда получается делегировать управление ресурсом, но продолжать оставаться хозяином. Правда вот в данной инструкции эти две роли пришлось совместить: самому управлять и самому владеть.

В общем, признание и реализация ценности "владения" — это тренд. Пока ещё небольшой, но — по моим наблюдениям — он показывает уверенный рост.

Так что если вы решите вернуть себе право распоряжаться своими заметками с помощью Obsidian — вы знаете что делать.

Теги:

- [obsidian](https://habr.com/ru/search/?target_type=posts&order=relevance&q=[obsidian])
- [notion](https://habr.com/ru/search/?target_type=posts&order=relevance&q=[notion])
- [git](https://habr.com/ru/search/?target_type=posts&order=relevance&q=[git])
- [github](https://habr.com/ru/search/?target_type=posts&order=relevance&q=[github])
- [синхронизация](https://habr.com/ru/search/?target_type=posts&order=relevance&q=[%D1%81%D0%B8%D0%BD%D1%85%D1%80%D0%BE%D0%BD%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F])
- [backup](https://habr.com/ru/search/?target_type=posts&order=relevance&q=[backup])
- [note-taking](https://habr.com/ru/search/?target_type=posts&order=relevance&q=[note-taking])
- [заметки](https://habr.com/ru/search/?target_type=posts&order=relevance&q=[%D0%B7%D0%B0%D0%BC%D0%B5%D1%82%D0%BA%D0%B8])
- [fit](https://habr.com/ru/search/?target_type=posts&order=relevance&q=[fit])
- [владение](https://habr.com/ru/search/?target_type=posts&order=relevance&q=[%D0%B2%D0%BB%D0%B0%D0%B4%D0%B5%D0%BD%D0%B8%D0%B5])

Хабы:

- [Управление проектами](https://habr.com/ru/hubs/pm/)
- [GTD](https://habr.com/ru/hubs/gtd/)
- [Текстовые редакторы и IDE](https://habr.com/ru/hubs/txt_ide/)

+165

940

[144](https://habr.com/ru/articles/843288/comments/)[

](https://habr.com/ru/articles/843288/comments/)

### Редакторский дайджест

Присылаем лучшие статьи раз в месяц

Оставляя свою почту, я принимаю [Политику конфиденциальности](https://account.habr.com/ru/info/confidential) и даю согласие на получение рассылок

[

![](https://habrastorage.org/getpro/habr/avatars/bec/b76/cb8/becb76cb81598c0fff9c67130c1f6cff.png)

](https://habr.com/ru/users/neodavinchi/)

94

Карма

0.2

Рейтинг

Neo Davinchi [@neodavinchi](https://habr.com/ru/users/neodavinchi/)

Ѱ-Инженер

Подписаться

[Telegram](https://telegram.me/neodavinchi)

[](https://yandex.ru/an/count/WliejI_zOoVX2LcH0sqN05Edbqe5TgK2MsVaNWBc1AfummJq9juMCY42tWi8w2P4CC45h5Gg3-W2VJwNEtkstxdpNLyxNx_ZzOOxN6SFr2V5dG85zFcyU-pSfoT_EaiG8LcLe50xYajjgMrFdRVJTaghIqP3GIEAZ8I6h4f5GkSTC6MJ0HOko8mxIfzxLQrQNGmwZejyV7nyV4ekGDAZ0Eq5IBG5Q2-GKrA0zW8vlWAq5yWbAK1xWTnU0LfeaZod9pu2zQWAo5R0f4dEru4OBRyCCZJJd2xTboN4nuSmpmUlzy0rCVx0W9When03VENF2YXuzXmvHU01ag023lP9lQtEt4gzEf_L6gTEyzffnV4Q2eH7w_lEfxUK2hG4rIIbH0t8bRoR_NkNGG20Ayxd6oORRmMdG9lT1pRP0FORlW5iuFehDoPcPsruYr-xXXs3Be3N0LtVzHgT0vYMif0dZKs4q72RDDili1XW330a03RbACm9XH8Y1F3bW5QxWxbOQTpXrODB52mOj3D60h7IuYwO2_WZejRK53ZNPs5hOSSDbaB3b2bmBSHvXSMGcxhUZD4Ty9Egmk1Dc03JBK0ioWZE9IyAcdhBikQtWbDWarl1AR2v-z_OZED-CGKuxWyZC3NrmJbGPlzResZaUsiKZNmkXxFYtmEWuyhNNc0k-zaBp6LCym1Op39pwVLcPBStkX7dSIwFtd4Uv_BEshoP7lHbtzS5IDQJVnUIhEx7j0SZG6vqs_FV4Y8dnsz-I-kwVewS7BzvhLUYQmaPuhCJ31a2_Wjl8UcYuPXaSwLIgPm1vEW3czb3-PECBj30oasHQ3VyaVh0VdP8cRE9xsPS8pMKLpCCM0g3Fn6OPFmKeJWldcdccHeEbKhs7d4oZZwij4pmyXo6zQoEwKfiI5zCNIXX5_1Be4FIyDkDxUHPRs49L55V9VUOFH2WmV2VMJOCm0bDtLIfjNzn1MzmLEq83TfrrkQMgp0gRic2c6dmns_8OyH1VXVlBwmKWamc~2?test-tag=3386495813550097&banner-sizes=eyI3MjA1NzYxMTIxMjU0MzUxNSI6IjMwMHgyNTAifQ%3D%3D&ctime=1755148382977&actual-format=13&pcodever=1301520&banner-test-tags=eyI3MjA1NzYxMTIxMjU0MzUxNSI6IjQyOTUyMjEyOTcifQ%3D%3D&rendered-direct-assets=eyI3MjA1NzYxMTIxMjU0MzUxNSI6MTA0ODYzM30&width=300&height=250&stat-id=1&pcode-active-testids=1333267%2C0%2C30%3B1336752%2C0%2C68&subDesignId=1000870001)

[

![](https://avatars.mds.yandex.net/get-direct/4303781/8gq5CFJAUpGPN-eTrHpFrg/wx1080)







](https://yandex.ru/an/count/WliejI_zOoVX2LcH0sqN05Edbqe5TgK2MsVaNWBc1AfummJq9juMCY42tWi8w2P4CC45h5Gg3-W2VJwNEtkstxdpNLyxNx_ZzOOxN6SFr2V5dG85zFcyU-pSfoT_EaiG8LcLe50xYajjgMrFdRVJTaghIqP3GIEAZ8I6h4f5GkSTC6MJ0HOko8mxIfzxLQrQNGmwZejyV7nyV4ekGDAZ0Eq5IBG5Q2-GKrA0zW8vlWAq5yWbAK1xWTnU0LfeaZod9pu2zQWAo5R0f4dEru4OBRyCCZJJd2xTboN4nuSmpmUlzy0rCVx0W9When03VENF2YXuzXmvHU01ag023lP9lQtEt4gzEf_L6gTEyzffnV4Q2eH7w_lEfxUK2hG4rIIbH0t8bRoR_NkNGG20Ayxd6oORRmMdG9lT1pRP0FORlW5iuFehDoPcPsruYr-xXXs3Be3N0LtVzHgT0vYMif0dZKs4q72RDDili1XW330a03RbACm9XH8Y1F3bW5QxWxbOQTpXrODB52mOj3D60h7IuYwO2_WZejRK53ZNPs5hOSSDbaB3b2bmBSHvXSMGcxhUZD4Ty9Egmk1Dc03JBK0ioWZE9IyAcdhBikQtWbDWarl1AR2v-z_OZED-CGKuxWyZC3NrmJbGPlzResZaUsiKZNmkXxFYtmEWuyhNNc0k-zaBp6LCym1Op39pwVLcPBStkX7dSIwFtd4Uv_BEshoP7lHbtzS5IDQJVnUIhEx7j0SZG6vqs_FV4Y8dnsz-I-kwVewS7BzvhLUYQmaPuhCJ31a2_Wjl8UcYuPXaSwLIgPm1vEW3czb3-PECBj30oasHQ3VyaVh0VdP8cRE9xsPS8pMKLpCCM0g3Fn6OPFmKeJWldcdccHeEbKhs7d4oZZwij4pmyXo6zQoEwKfiI5zCNIXX5_1Be4FIyDkDxUHPRs49L55V9VUOFH2WmV2VMJOCm0bDtLIfjNzn1MzmLEq83TfrrkQMgp0gRic2c6dmns_8OyH1VXVlBwmKWamc~2?test-tag=3386495813550097&banner-sizes=eyI3MjA1NzYxMTIxMjU0MzUxNSI6IjMwMHgyNTAifQ%3D%3D&ctime=1755148382977&actual-format=13&pcodever=1301520&banner-test-tags=eyI3MjA1NzYxMTIxMjU0MzUxNSI6IjQyOTUyMjEyOTcifQ%3D%3D&rendered-direct-assets=eyI3MjA1NzYxMTIxMjU0MzUxNSI6MTA0ODYzM30&width=300&height=250&stat-id=1&pcode-active-testids=1333267%2C0%2C30%3B1336752%2C0%2C68&subDesignId=1000870001)

[

![](https://favicon.yandex.net/favicon/intelion.cloud?size=120&stub=2)

intelion.cloud







](https://yandex.ru/an/count/WliejI_zOoVX2LcH0sqN05Edbqe5TgK2MsVaNWBc1AfummJq9juMCY42tWi8w2P4CC45h5Gg3-W2VJwNEtkstxdpNLyxNx_ZzOOxN6SFr2V5dG85zFcyU-pSfoT_EaiG8LcLe50xYajjgMrFdRVJTaghIqP3GIEAZ8I6h4f5GkSTC6MJ0HOko8mxIfzxLQrQNGmwZejyV7nyV4ekGDAZ0Eq5IBG5Q2-GKrA0zW8vlWAq5yWbAK1xWTnU0LfeaZod9pu2zQWAo5R0f4dEru4OBRyCCZJJd2xTboN4nuSmpmUlzy0rCVx0W9When03VENF2YXuzXmvHU01ag023lP9lQtEt4gzEf_L6gTEyzffnV4Q2eH7w_lEfxUK2hG4rIIbH0t8bRoR_NkNGG20Ayxd6oORRmMdG9lT1pRP0FORlW5iuFehDoPcPsruYr-xXXs3Be3N0LtVzHgT0vYMif0dZKs4q72RDDili1XW330a03RbACm9XH8Y1F3bW5QxWxbOQTpXrODB52mOj3D60h7IuYwO2_WZejRK53ZNPs5hOSSDbaB3b2bmBSHvXSMGcxhUZD4Ty9Egmk1Dc03JBK0ioWZE9IyAcdhBikQtWbDWarl1AR2v-z_OZED-CGKuxWyZC3NrmJbGPlzResZaUsiKZNmkXxFYtmEWuyhNNc0k-zaBp6LCym1Op39pwVLcPBStkX7dSIwFtd4Uv_BEshoP7lHbtzS5IDQJVnUIhEx7j0SZG6vqs_FV4Y8dnsz-I-kwVewS7BzvhLUYQmaPuhCJ31a2_Wjl8UcYuPXaSwLIgPm1vEW3czb3-PECBj30oasHQ3VyaVh0VdP8cRE9xsPS8pMKLpCCM0g3Fn6OPFmKeJWldcdccHeEbKhs7d4oZZwij4pmyXo6zQoEwKfiI5zCNIXX5_1Be4FIyDkDxUHPRs49L55V9VUOFH2WmV2VMJOCm0bDtLIfjNzn1MzmLEq83TfrrkQMgp0gRic2c6dmns_8OyH1VXVlBwmKWamc~2?test-tag=3386495813550097&banner-sizes=eyI3MjA1NzYxMTIxMjU0MzUxNSI6IjMwMHgyNTAifQ%3D%3D&ctime=1755148382977&actual-format=13&pcodever=1301520&banner-test-tags=eyI3MjA1NzYxMTIxMjU0MzUxNSI6IjQyOTUyMjEyOTcifQ%3D%3D&rendered-direct-assets=eyI3MjA1NzYxMTIxMjU0MzUxNSI6MTA0ODYzM30&width=300&height=250&stat-id=1&pcode-active-testids=1333267%2C0%2C30%3B1336752%2C0%2C68&subDesignId=1000870001)

Реклама•16+

[

Аренда удалённых видеокарт Nvidia от 30₽/ч. Пауза 0₽

](https://yandex.ru/an/count/WliejI_zOoVX2LcH0sqN05Edbqe5TgK2MsVaNWBc1AfummJq9juMCY42tWi8w2P4CC45h5Gg3-W2VJwNEtkstxdpNLyxNx_ZzOOxN6SFr2V5dG85zFcyU-pSfoT_EaiG8LcLe50xYajjgMrFdRVJTaghIqP3GIEAZ8I6h4f5GkSTC6MJ0HOko8mxIfzxLQrQNGmwZejyV7nyV4ekGDAZ0Eq5IBG5Q2-GKrA0zW8vlWAq5yWbAK1xWTnU0LfeaZod9pu2zQWAo5R0f4dEru4OBRyCCZJJd2xTboN4nuSmpmUlzy0rCVx0W9When03VENF2YXuzXmvHU01ag023lP9lQtEt4gzEf_L6gTEyzffnV4Q2eH7w_lEfxUK2hG4rIIbH0t8bRoR_NkNGG20Ayxd6oORRmMdG9lT1pRP0FORlW5iuFehDoPcPsruYr-xXXs3Be3N0LtVzHgT0vYMif0dZKs4q72RDDili1XW330a03RbACm9XH8Y1F3bW5QxWxbOQTpXrODB52mOj3D60h7IuYwO2_WZejRK53ZNPs5hOSSDbaB3b2bmBSHvXSMGcxhUZD4Ty9Egmk1Dc03JBK0ioWZE9IyAcdhBikQtWbDWarl1AR2v-z_OZED-CGKuxWyZC3NrmJbGPlzResZaUsiKZNmkXxFYtmEWuyhNNc0k-zaBp6LCym1Op39pwVLcPBStkX7dSIwFtd4Uv_BEshoP7lHbtzS5IDQJVnUIhEx7j0SZG6vqs_FV4Y8dnsz-I-kwVewS7BzvhLUYQmaPuhCJ31a2_Wjl8UcYuPXaSwLIgPm1vEW3czb3-PECBj30oasHQ3VyaVh0VdP8cRE9xsPS8pMKLpCCM0g3Fn6OPFmKeJWldcdccHeEbKhs7d4oZZwij4pmyXo6zQoEwKfiI5zCNIXX5_1Be4FIyDkDxUHPRs49L55V9VUOFH2WmV2VMJOCm0bDtLIfjNzn1MzmLEq83TfrrkQMgp0gRic2c6dmns_8OyH1VXVlBwmKWamc~2?test-tag=3386495813550097&banner-sizes=eyI3MjA1NzYxMTIxMjU0MzUxNSI6IjMwMHgyNTAifQ%3D%3D&ctime=1755148382977&actual-format=13&pcodever=1301520&banner-test-tags=eyI3MjA1NzYxMTIxMjU0MzUxNSI6IjQyOTUyMjEyOTcifQ%3D%3D&rendered-direct-assets=eyI3MjA1NzYxMTIxMjU0MzUxNSI6MTA0ODYzM30&width=300&height=250&stat-id=1&pcode-active-testids=1333267%2C0%2C30%3B1336752%2C0%2C68&subDesignId=1000870001)

[](https://yandex.ru/an/count/WliejI_zOoVX2LcH0sqN05Edbqe5TgK2MsVaNWBc1AfummJq9juMCY42tWi8w2P4CC45h5Gg3-W2VJwNEtkstxdpNLyxNx_ZzOOxN6SFr2V5dG85zFcyU-pSfoT_EaiG8LcLe50xYajjgMrFdRVJTaghIqP3GIEAZ8I6h4f5GkSTC6MJ0HOko8mxIfzxLQrQNGmwZejyV7nyV4ekGDAZ0Eq5IBG5Q2-GKrA0zW8vlWAq5yWbAK1xWTnU0LfeaZod9pu2zQWAo5R0f4dEru4OBRyCCZJJd2xTboN4nuSmpmUlzy0rCVx0W9When03VENF2YXuzXmvHU01ag023lP9lQtEt4gzEf_L6gTEyzffnV4Q2eH7w_lEfxUK2hG4rIIbH0t8bRoR_NkNGG20Ayxd6oORRmMdG9lT1pRP0FORlW5iuFehDoPcPsruYr-xXXs3Be3N0LtVzHgT0vYMif0dZKs4q72RDDili1XW330a03RbACm9XH8Y1F3bW5QxWxbOQTpXrODB52mOj3D60h7IuYwO2_WZejRK53ZNPs5hOSSDbaB3b2bmBSHvXSMGcxhUZD4Ty9Egmk1Dc03JBK0ioWZE9IyAcdhBikQtWbDWarl1AR2v-z_OZED-CGKuxWyZC3NrmJbGPlzResZaUsiKZNmkXxFYtmEWuyhNNc0k-zaBp6LCym1Op39pwVLcPBStkX7dSIwFtd4Uv_BEshoP7lHbtzS5IDQJVnUIhEx7j0SZG6vqs_FV4Y8dnsz-I-kwVewS7BzvhLUYQmaPuhCJ31a2_Wjl8UcYuPXaSwLIgPm1vEW3czb3-PECBj30oasHQ3VyaVh0VdP8cRE9xsPS8pMKLpCCM0g3Fn6OPFmKeJWldcdccHeEbKhs7d4oZZwij4pmyXo6zQoEwKfiI5zCNIXX5_1Be4FIyDkDxUHPRs49L55V9VUOFH2WmV2VMJOCm0bDtLIfjNzn1MzmLEq83TfrrkQMgp0gRic2c6dmns_8OyH1VXVlBwmKWamc~2?test-tag=3386495813550097&banner-sizes=eyI3MjA1NzYxMTIxMjU0MzUxNSI6IjMwMHgyNTAifQ%3D%3D&ctime=1755148382977&actual-format=13&pcodever=1301520&banner-test-tags=eyI3MjA1NzYxMTIxMjU0MzUxNSI6IjQyOTUyMjEyOTcifQ%3D%3D&rendered-direct-assets=eyI3MjA1NzYxMTIxMjU0MzUxNSI6MTA0ODYzM30&width=300&height=250&stat-id=1&pcode-active-testids=1333267%2C0%2C30%3B1336752%2C0%2C68&subDesignId=1000870001)

[Комментарии 144](https://habr.com/ru/articles/843288/comments/)

## Публикации

Лучшие за суткиПохожие5 созвонов в день — пора увольняться. Или начать делать это](https://habr.com/ru/companies/yougile/articles/936700/)
    
    Простой
    
    7 мин
    
    17K
    
    Мнение
    
    +50
    
    59
    
    [36](https://habr.com/ru/companies/yougile/articles/936700/comments/)[
    
    ](https://habr.com/r




[![](https://habrastorage.org/r/w390/getpro/habr/upload_files/9c4/739/ca2/9c4739ca23615541ee739a29706eb1cd.jpg)
##### Подключение своего css файла

- Скопировать свой css файл в /home/kvl/obsidian/.obsidian/snippets
- Пример содержания файла (как обычный css файл)

![[Снимок экрана от 2025-04-25 11-41-31.png]]
- В программе obsidian выбрать настройки (в левом нижнем углу)
- Оформление -> Фрагменты CSS кода
- Нажать обновить
- Отобразится наш файл
- Использование в документе obsidian:

![[Снимок экрана от 2025-04-25 11-44-49 1.png]]
