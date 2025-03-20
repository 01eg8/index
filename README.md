Домашнее задание к занятию «Индексы» Макин Олег


Задание 1

Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

Задание 2

Выполните explain analyze следующего запроса:

    select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
    from payment p, rental r, customer c, inventory i, film f
    where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id

перечислите узкие места;

оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.

Дополнительные задания (со звёздочкой*)

Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

Задание 3*

Самостоятельно изучите, какие типы индексов используются в PostgreSQL. Перечислите те индексы, которые используются в PostgreSQL, а в MySQL — нет.

Приведите ответ в свободной форме.




### Задание 1

    SELECT SUM(index_length) / SUM(data_length) * 100
    FROM INFORMATION_SCHEMA.TABLES

![img](https://github.com/01eg8/index/blob/main/img/Screenshot%20from%202025-03-20%2023-26-51.png)

### Задание 2

![img](https://github.com/01eg8/index/blob/main/img/Screenshot%20from%202025-03-20%2023-33-46.png)

В данном задании узкие места: 
- таблица film и inventory не используются при работе запроса
- выражение dictinct и SUM OVER можно убрать, и использовать GROUP BY
- лучше использовать join 


    SELECT concat(c.last_name, ' ', c.first_name), SUM(p.amount)
    FROM customer c
    JOIN rental r ON c.customer_id = r.customer_id 
    JOIN payment p ON r.rental_date = p.payment_date 
    WHERE date(p.payment_date) = '2005-07-30'
    GROUP BY c.customer_id


![img](https://github.com/01eg8/index/blob/main/img/Screenshot%20from%202025-03-20%2023-37-10.png)

для ускорения работы нужно создать индекс


    CREATE INDEX payment_temp ON payment(payment_date)


![img](https://github.com/01eg8/index/blob/main/img/Screenshot%20from%202025-03-20%2023-38-42.png)
![img](https://github.com/01eg8/index/blob/main/img/Screenshot%20from%202025-03-20%2023-39-36.png)



### Задание 3

Индексы, которые используются в PostgreSQL, но не в MySQL:

- Bitmap index. Метод битовых индексов заключается в создании отдельных битовых карт (последовательность 0 и 1) для каждого возможного значения столбца. 
- Partial index. Индекс, построенный на части таблицы, удовлетворяющей определённому условию самого индекса.  
- Function based index. Индексы, ключи которых хранят результат пользовательских функций. 




