# JOIN-ы в SQL: как они работают

JOIN в SQL - это операция, которая позволяет комбинировать данные из двух или более таблиц на основе связанных между ними столбцов. Вот основные типы JOIN и как они работают:
# Основные типы JOIN
## INNER JOIN (или просто JOIN)
   - Возвращает только строки, где есть совпадение в обеих таблицах
   - Синтаксис:
     ```sql
     SELECT columns
     FROM table1
     INNER JOIN table2 ON table1.column = table2.column;
     ```
## LEFT JOIN (LEFT OUTER JOIN)
   - Возвращает все строки из левой таблицы и совпадающие из правой
   - Если совпадений нет, справа будут NULL
   - Синтаксис:
     ```sql
     SELECT columns
     FROM table1
     LEFT JOIN table2 ON table1.column = table2.column;
     ```
## RIGHT JOIN (RIGHT OUTER JOIN)
   - Возвращает все строки из правой таблицы и совпадающие из левой
   - Если совпадений нет, слева будут NULL
   - Синтаксис:
     ```sql
     SELECT columns
     FROM table1
     RIGHT JOIN table2 ON table1.column = table2.column;
     ```
## FULL JOIN (FULL OUTER JOIN)
   - Возвращает все строки из обеих таблиц
   - Если совпадений нет, недостающие значения будут NULL
   - Синтаксис:
     ```sql
     SELECT columns
     FROM table1
     FULL JOIN table2 ON table1.column = table2.column;
     ```
## CROSS JOIN
   - Возвращает декартово произведение таблиц (все возможные комбинации строк)
   - Не требует условия ON
   - Синтаксис:
     ```sql
     SELECT columns
     FROM table1
     CROSS JOIN table2;
     ```
# Как работает JOIN
При выполнении JOIN:
1. СУБД берет каждую строку из первой таблицы
2. Ищет совпадающие строки во второй таблице согласно условию ON
3. Формирует результирующую строку, объединяя данные из обеих таблиц
4. Для OUTER JOIN добавляет строки без совпадений с NULL значениями
## Примеры
```sql
-- INNER JOIN: только сотрудники с отделами
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d ON e.department_id = d.id;

-- LEFT JOIN: все сотрудники, даже без отдела
SELECT e.name, d.department_name
FROM employees e
LEFT JOIN departments d ON e.department_id = d.id;
```

JOIN-ы могут быть каскадными (соединять несколько таблиц) и могут включать сложные условия соединения.
# Относительно Postgres
В PostgreSQL поддерживаются все стандартные типы JOIN из SQL, но есть некоторые особенности и дополнительные возможности. Вот полный обговор JOIN в PostgreSQL:
## Поддерживаемые JOIN в PostgreSQL
### Базовые JOIN-ы (как в стандарте SQL):
   - `INNER JOIN` (или просто `JOIN`)
   - `LEFT [OUTER] JOIN`
   - `RIGHT [OUTER] JOIN`
   - `FULL [OUTER] JOIN`
   - `CROSS JOIN`
### Специальные формы JOIN:
   - `NATURAL JOIN` - автоматически соединяет по одноименным столбцам
   - `LATERAL JOIN` - позволяет обращаться к столбцам предыдущих таблиц в подзапросе
### Дополнительные возможности:
   - JOIN с USING - упрощенный синтаксис при совпадении имен столбцов
   - JOIN с подзапросами (включая LATERAL)
   - JOIN с табличными функциями
## Примеры всех типов JOIN в PostgreSQL
### INNER JOIN
```sql
SELECT o.order_id, c.customer_name
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id;
```
### LEFT JOIN
```sql
SELECT p.product_name, o.order_id
FROM products p
LEFT JOIN order_details o ON p.product_id = o.product_id;
```
### RIGHT JOIN
```sql
SELECT d.department_name, e.employee_name
FROM employees e
RIGHT JOIN departments d ON e.department_id = d.department_id;
```
### FULL JOIN
```sql
SELECT a.actor_name, m.movie_title
FROM actors a
FULL JOIN movies m ON a.movie_id = m.movie_id;
```
### CROSS JOIN
```sql
SELECT s.size, c.color
FROM sizes s
CROSS JOIN colors c;
```
### NATURAL JOIN (автоматически по общим столбцам)
```sql
SELECT *
FROM products
NATURAL JOIN inventories;
```
### JOIN с USING (когда столбцы имеют одинаковые имена)
```sql
SELECT *
FROM employees
JOIN departments USING (department_id);
```
### LATERAL JOIN (особенность PostgreSQL)
```sql
SELECT u.user_id, l.login_time
FROM users u,
LATERAL (
    SELECT login_time
    FROM logins
    WHERE user_id = u.user_id
    ORDER BY login_time DESC
    LIMIT 3
) l;
```
## Особенности PostgreSQL
1. **Оптимизация JOIN**: PostgreSQL имеет сложный оптимизатор запросов, который выбирает лучший план выполнения для JOIN.
2. **Поддержка сложных условий**: Можно использовать сложные выражения в условиях JOIN.
3. **LATERAL JOIN**: Уникальная возможность ссылаться на предыдущие таблицы в подзапросе.
4. **JOIN с табличными функциями**: Можно соединять таблицы с результатами функций.

PostgreSQL поддерживает все стандартные JOIN-операции SQL, а также добавляет свои расширения, что делает его очень мощным для работы с реляционными данными.
