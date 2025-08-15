### Основные команды

`unnest` - предназначена для разворачивания массивов и превращения их в набор строк
[ссылка](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257135/)

```
SELECT unnest(ARRAY['one','two','three'])
```

> [!NOTE] Результат:
> one 
>two 
>three

### Краткие выводы

> [!NOTE] Ключевые слова
>SELECT -- перечисление полей результирующей таблицы 
>FROM -- указание источника данных 
>WHERE -- фильтрация данных 
>GROUP BY -- группировка данных 
>HAVING -- фильтрация данных после группировки 
>ORDER BY -- сортировка результирующей таблицы 
>LIMIT -- ограничение количества выводимых записей

- Научились составлять **подзапросы** [[#УРОК 7]] и узнали, что их можно применять в блоках `SELECT`, `FROM`, `WHERE` и `HAVING`.
### УРОК 7

 ***Задание 19***
[ссылка на задание](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/353799/)

Посчитайте возраст каждого пользователя в таблице `users`.

Возраст измерьте числом полных лет, как мы делали [в прошлых уроках](https://lab.karpov.courses/learning/152/module/1762/lesson/17927/54325/256089/). Возраст считайте относительно последней даты в таблице `user_actions`.

Для тех пользователей, у которых в таблице `users` не указана дата рождения, укажите среднее значение возраста всех остальных пользователей, округлённое до целого числа.

Колонку с возрастом назовите `age`. В результат включите колонки с id пользователя и возрастом. Отсортируйте полученный результат по возрастанию id пользователя.

Поля в результирующей таблице: `user_id`, `age`

---

**Пояснение:**

В этой задаче вам придётся написать несколько подзапросов и, возможно, использовать табличные выражения. Пригодятся функции `DATE_PART`, `AGE` и `COALESCE`.

Функцию `COALESCE` мы рассматривали [в первых уроках](https://lab.karpov.courses/learning/152/module/1762/lesson/18484/53190/250926/).

Основная сложность заключается в заполнении пропусков средним значением — подумайте, как это можно сделать, и постройте запрос вокруг своего подхода.

**Решение:**
```
with users_age as 
 (SELECT 
    user_id,
    date_part('year', age((SELECT max(time)
                          FROM   user_actions), birth_date)) as age
  FROM   users)
SELECT user_id,
       coalesce(age, (SELECT round(avg(age)) FROM   users_age))::integer as age
FROM   users_age
ORDER BY user_id
```

***Задание 20***
[ссылка на задание](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/353800/)

Для каждого заказа, в котором больше 5 товаров, рассчитайте время, затраченное на его доставку. 

В результат включите id заказа, время принятия заказа курьером, время доставки заказа и время, затраченное на доставку. Новые колонки назовите соответственно `time_accepted`, `time_delivered` и `delivery_time`.

В расчётах учитывайте только неотменённые заказы. Время, затраченное на доставку, выразите в минутах, округлив значения до целого числа. Результат отсортируйте по возрастанию id заказа.

Поля в результирующей таблице: `order_id`, `time_accepted`, `time_delivered` и `delivery_time`

---

**Пояснение:**

Чтобы перевести значение интервала в минуты, необходимо извлечь из него количество секунд, а затем поделить это значение на количество секунд в одной минуте. Для извлечения количества секунд из интервала можно воспользоваться следующей конструкцией:

SELECT EXTRACT(epoch FROM INTERVAL '3 days, 1:21:32') Результат: 264092

Функция `EXTRACT` работает аналогично функции `DATE_PART`, которую мы рассматривали [на прошлых уроках](https://lab.karpov.courses/learning/152/module/1762/lesson/18484/53190/250925/), но имеет несколько иной синтаксис. Попробуйте воспользоваться функцией `EXTRACT` в этой задаче.

---

**На заметку:**

Подробнее про функцию `EXTRACT` можно почитать [здесь](https://www.postgresqltutorial.com/postgresql-date-functions/postgresql-extract/).

**Решение:**
```
with current_order_5 as (SELECT order_id
                         FROM   orders
                         WHERE  array_length(product_ids, 1) > 5
                            and order_id not in (SELECT order_id
                                              FROM   user_actions
                                              WHERE  action = 'cancel_order')
)
SELECT order_id,
     min(time) as time_accepted,
     max(time) as time_delivered,
     ((extract(epoch FROM max(time)) - extract(epoch FROM min(time)))/60)::integer as delivery_time
FROM   courier_actions
WHERE  order_id in (SELECT order_id
                    FROM   current_order_5)
GROUP BY order_id
ORDER BY order_id asc    
```

***Задание 21***
[ссылка на задание](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/353801/)

При проведении анализа продаж принято разделять первые и повторные покупки. Повторные покупки мы пока отложим на потом и для начала разберёмся с тем, как можно посчитать первые.

---
Для каждой даты в таблице `user_actions` посчитайте количество первых заказов, совершённых пользователями.

Первыми заказами будем считать заказы, которые пользователи сделали в нашем сервисе впервые. В расчётах учитывайте только неотменённые заказы.

В результат включите две колонки: дату и количество первых заказов в эту дату. Колонку с датами назовите `date`, а колонку с первыми заказами — `first_orders`.

Результат отсортируйте по возрастанию даты.

Поля в результирующей таблице: `date`, `first_orders`

---

**Пояснение:**

Учитывайте, что у каждого пользователя может быть всего один первый заказ (что вполне логично).

**Решение:**
```
SELECT first_order_date as date,
       count(user_id) as first_orders
FROM   (SELECT user_id,
               min(time)::date as first_order_date
        FROM   user_actions
        WHERE  order_id not in (SELECT order_id
                                FROM   user_actions
                                WHERE  action = 'cancel_order')
        GROUP BY user_id) t
GROUP BY first_order_date
ORDER BY date
```

***Задание 22***
[ссылка на задание](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257135/)

Выберите все колонки из таблицы `orders` и дополнительно в качестве последней колонки укажите функцию `unnest`, применённую к колонке `product_ids`. Эту последнюю колонку назовите `product_id`. Больше ничего с данными делать не нужно.

Добавьте в запрос оператор `LIMIT` и выведите только первые 100 записей результирующей таблицы.

Поля в результирующей таблице: `creation_time`, `order_id`, `product_ids`, `product_id`

Посмотрите на результат работы функции `unnest` и постарайтесь разобраться, что произошло с исходной таблицей.

**Решение:**
```
SELECT
    creation_time,
    order_id,
    product_ids,
    UNNEST(product_ids) as product_id
FROM
    orders
LIMIT
    100
```

***Задание 23***
[ссылка на задание](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257136/)

Используя функцию `unnest`, определите 10 самых популярных товаров в таблице `orders`.

Самыми популярными товарами будем считать те, которые встречались в заказах чаще всего. Если товар встречается в одном заказе несколько раз (когда было куплено несколько единиц товара), это тоже учитывается при подсчёте. Учитывайте только неотменённые заказы.

Выведите id товаров и то, сколько раз они встречались в заказах (то есть сколько раз были куплены). Новую колонку с количеством покупок товаров назовите `times_purchased`.

Результат отсортируйте по возрастанию id товара.

Поля в результирующей таблице: `product_id`, `times_purchased`

---

**Пояснение:**

В этом задании вам необходимо сначала развернуть списки с товарами в заказах, а затем для каждого товара посчитать, сколько раз он встретился в заказах. Для определения самых популярных товаров используйте оператор `LIMIT`.

Обратите внимание, что отсортировать результат необходимо по колонке с id товара.

**Решение:**
```
with t1 as (
 SELECT
  unnest(product_ids) as product_id, count(order_id) as times_purchased
 FROM
  orders
 where
  order_id not in (SELECT order_id
                   FROM user_actions
                   WHERE action = 'cancel_order')
 group by
  product_id
 ORDER BY
  times_purchased desc
 LIMIT
  10
)

select
 product_id, 
 times_purchased
from 
 t1
order by 
 product_id asc
```

***Задание 24***
[ссылка на задание](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257137/)

Из таблицы `orders` выведите id и содержимое заказов, которые включают хотя бы один из пяти самых дорогих товаров, доступных в нашем сервисе.

Результат отсортируйте по возрастанию id заказа.

Поля в результирующей таблице: `order_id`, `product_ids`

---

**Пояснение:**

В этой задаче вам снова пригодится функция `unnest`. Также для упрощения кода можно использовать табличные выражения.

**Решение:**
```
with t1 as (
 select 
  unnest(product_ids) as product_for_one, 
  order_id as order_id, product_ids
 from orders
)

select
  distinct order_id, 
  product_ids  
from t1
where product_for_one in (select product_id 
                          from products 
                          order by price 
                          desc limit 5)
order by 
 order_id asc
```

**Решение с сайта (использование нескольких таблиц):**
```
with top_products as (SELECT product_id
                      FROM   products
                      ORDER BY price desc limit 5), unnest as (SELECT order_id,
                                                product_ids,
                                                unnest(product_ids) as product_id
                                         FROM   orders)
SELECT DISTINCT order_id,
                product_ids
FROM   unnest
WHERE  product_id in (SELECT product_id
                      FROM   top_products)
ORDER BY order_id
```