```
# Справка по комманде
curl --help category
curl help https
```

```
# выполняет HTTP-запрос get и отображает статическое содержимое страницы
curl https://google.com
curl https://google.com:8443
```

```
# Скачать файл и сохранить под оригинальным названием
curl -O https://testdomain.com/testfile.tar.gz
```

```
# Скачать файл
curl https://testdomain.com/testfile.tar.gz > name_file.gz
```

```
# Скачать файл с добавочным заголвком (т.е. сервер вернёт файл в архивированном виде)
curl -H 'Accept-Encoding: gzip,deflare' https://testdomain.com/testfile.tar.gz > name_file.gz
```

![[Снимок экрана от 2025-06-17 15-03-55.png]]

```
# Посмотреть, какие заголовки отдает сервер
curl -I https:/google.com
```

```
# Запрос с добавлением имени сайта
curl -H "Host: example.com" https://your-domain.com/path
```

```
# Отправка запроса с добавлением имени сайта и query пареметрами через адресную строку и выводом статического содержимого страницы
curl -H "Host: test.example.local" 'http://127.0.0.1/api/?type=PDO&firstname=Vladimir&year=1983'
```

![[Снимок экрана от 2025-12-03 16-00-04 1.png]]

```
# Посмотреть, какие заголовки отдает сервер, при запросе с добавленным в ручну заголовком
curl -I -H 'Accept-Encoding: gzip,deflare' https:/google.com
```

![[Снимок экрана от 2025-06-17 14-38-46.png]]

```
# Отправка запроса с query параметрами и выводов всех заголовков
curl http://127.0.0.1:8080/response-headers\?test\=test\&server\=httpbin -H "Host: new.httpbin.local" -v
```
![[Снимок экрана от 2025-06-25 16-24-13.png]]


```
# Отправка запроса с телом запроса в виде json
curl -X POST -H "Host: test.example.local" -H "Content-Type: application/json" -d '{"type": "PG", "firstname": "Vladimir1", "year": "1983"}' http://127.0.0.1/api/
```

![[Снимок экрана от 2025-12-03 16-01-23 1.png]]

```
# Получение и сохранение токена по логину и паролю (сохраняется в файл cookies.txt)
curl -c cookies.txt -X POST -H "Content-Type: application/json" -d '{"login": "логин", "password": "пароль"}' https://aiplatform.mos.ru/app_login
```

```
curl -vvv --location --request POST "https://aiplatform.mos.ru/app_login" --header 'Content-Type: application/json' --data-raw '{"login": "логин", "password":"пароль"}' -b cookie.txt -c cookie.txt
```
- [ ] 
```
# Скрыть вывод, оставить только код ответа
curl -s -o /dev/null -w "%{http_code}" -X POST "https://models.aiplatform.mos.ru/operation/mistralsmall24b/v1/chat/completions" -H "Content-Type: application/json" -b 'cookie.txt' -c 'cookie.txt' -d '{
    "model": "local_huggingface/Mistral-Small-3.2-24B-Instruct-2506",
    "messages": [
      {
        "role": "user",
        "content": "Привет!"
      }
    ],
    "max_tokens": 150,
    "temperature": 0.3 }'

```

{"login": "sys_udrvs_prod_ai_helper_ai", "password": "J*YkKiewL7GKJkBG"}
Content-Type
application/json



{"model": "local_huggingface/Mistral-Small-3.2-24B-Instruct-2506",  "messages": [ { "role": "user",  "content": "Привет!" } ], "max_tokens": 150, "temperature": 0.3}

Content-Type         application/json
***Назад:*** [[os/сеть/!------Содержание------!]]
