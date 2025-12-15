# Домашнее задание к занятию "`SQL. Часть 2`" - `Юрочкин В.А.`

### Задание 1
Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию:

фамилия и имя сотрудника из этого магазина;
город нахождения магазина;
количество пользователей, закреплённых в этом магазине.

`Ответ:`
Ниже пример одного SQL-запроса, который вернёт сотрудника (фамилия+имя), город магазина и количество покупателей, обслуживаемых магазином, только для магазинов с числом покупателей > 300.
Предполагаю типовую схему: store → address → city, staff привязан к store, покупатели customer привязаны к store.

```
SELECT
CONCAT(st.first_name, ' ', st.last_name) AS staff_full_name,
c.city                                   AS store_city,
COUNT(cu.customer_id)                    AS customers_count
FROM store s
JOIN staff   st ON st.store_id = s.store_id
JOIN address a  ON a.address_id = s.address_id
JOIN city    c  ON c.city_id = a.city_id
JOIN customer cu ON cu.store_id = s.store_id
GROUP BY
st.first_name, st.last_name,
c.city
HAVING COUNT(cu.customer_id) > 300;
```
---

### Задание 2
Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

`решение одним SQL-запросом.`

```
SELECT COUNT(*) AS films_count
FROM film
WHERE length > (
SELECT AVG(length)
FROM film
);
```
---

### Задание 3
Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

`решение одним SQL-запросом.`

```
SELECT
DATE_FORMAT(p.payment_date, '%Y-%m') AS payment_month,
SUM(p.amount)                        AS total_payments,
COUNT(DISTINCT r.rental_id)          AS rentals_count
FROM payment p
JOIN rental r ON r.rental_id = p.rental_id
GROUP BY DATE_FORMAT(p.payment_date, '%Y-%m')
ORDER BY total_payments DESC
LIMIT 1;
```
Что делает запрос
агрегирует платежи по месяцам (YEAR-MONTH);
считает общую сумму платежей за каждый месяц;
считает количество аренд, относящихся к этому месяцу;
возвращает месяц с максимальной суммой платежей.
В результате вы получите:
месяц (в формате YYYY-MM);
сумму платежей за этот месяц;
количество аренд за этот месяц.
