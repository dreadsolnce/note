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

***Назад:*** [[os/сеть/!------Содержание------!]]
