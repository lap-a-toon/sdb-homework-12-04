# Домашнее задание к занятию 12.4 "Реляционные базы данных: SQL. Часть 2" - Лабазов Александр

---

### Задание 1.

Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей и выведите в результат следующую информацию: 
- фамилия и имя сотрудника из этого магазина,
- город нахождения магазина,
- количество пользователей, закрепленных в этом магазине.

```SQL
SELECT
	s.store_id,
	s2.staff_id,
	CONCAT(s2.first_name,' ',s2.last_name) staff_fio,
	c2.city,
	( SELECT COUNT(1) FROM customer c WHERE c.store_id =s.store_id ) customers_count
FROM 
	store s 
LEFT JOIN staff s2 ON s2.store_id =s.store_id
LEFT JOIN address a ON s.address_id =a.address_id
LEFT JOIN city c2 ON c2.city_id =a.city_id 
WHERE
	( SELECT COUNT(1) FROM customer c WHERE c.store_id =s.store_id ) > 300
;
```

### Задание 2.

Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

```SQL
SELECT
	COUNT(1)
FROM 
	film f
WHERE
	f.`length` > (SELECT AVG(f2.`length`) FROM film f2)
;
```

### Задание 3.

Получите информацию, за какой месяц была получена наибольшая сумма платежей и добавьте информацию по количеству аренд за этот месяц.
```SQL
SELECT 
	summs.mon monn, summs.summ, 
	count(r.rental_id)
FROM
	(SELECT
		MONTH(p.payment_date) mon,
		SUM(p.amount) summ
	FROM payment p 
	GROUP BY MONTH(p.payment_date)
	ORDER BY summ DESC
	LIMIT 1
	) summs
left join rental r on MONTH(r.rental_date)=summs.mon
GROUP BY monn
;
```
Знаю, что кривенько, но вполне рабочий вариант

## Дополнительные задания (со звездочкой*)
Эти задания дополнительные (не обязательные к выполнению) и никак не повлияют на получение вами зачета по этому домашнему заданию. Вы можете их выполнить, если хотите глубже и/или шире разобраться в материале.

### Задание 4*.

Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», 
иначе должно быть значение «Нет».
```SQL
SELECT 
	CONCAT(YEAR(p.payment_date),'-',MONTH(p.payment_date)) mon,
	CONCAT(s.first_name,' ',s.last_name) staff_name,
	SUM(p.amount) summa,
	IF(SUM(p.amount)>8000, 'Да', 'Нет') as premiya
FROM payment p 
LEFT JOIN staff s ON s.staff_id =p.staff_id 
GROUP BY mon, staff_name
;
```

### Задание 5*.

Найдите фильмы, которые ни разу не брали в аренду.
```SQL
SELECT 
	f.title 
FROM film f 
LEFT JOIN inventory i ON i.film_id =f.film_id
LEFT JOIN rental r ON r.inventory_id =i.inventory_id 
WHERE r.rental_id IS NULL 
;
```