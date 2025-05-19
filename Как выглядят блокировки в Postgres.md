В PostgreSQL есть несколько способов явно управлять блокировками для контроля конкурентного доступа к данным. Вот основные варианты:
# Блокировки строк (Row-Level Locks)
## FOR UPDATE (эксклюзивная блокировка)
```sql
SELECT * FROM table WHERE id = 1 FOR UPDATE;
```
- Блокирует строку для изменения
- Другие транзакции не могут заблокировать те же строки (ждут или получают ошибку)
## FOR NO KEY UPDATE
```sql
SELECT * FROM table WHERE id = 1 FOR NO KEY UPDATE;
```
- Более легкая версия FOR UPDATE
- Блокирует строку, но позволяет другим транзакциям читать данные
## FOR SHARE (блокировка для чтения)
```sql
SELECT * FROM table WHERE id = 1 FOR SHARE;
```
- Разрешает другим транзакциям читать данные
- Запрещает другим транзакциям изменять строку
## FOR KEY SHARE
```sql
SELECT * FROM table WHERE id = 1 FOR KEY SHARE;
```
- Самая слабая блокировка
- Разрешает чтение и некоторые виды обновлений
- Запрещает только удаление или изменение ключевых полей
# Блокировки таблиц (Table-Level Locks)
## ACCESS SHARE
```sql
LOCK TABLE table_name IN ACCESS SHARE MODE;
```
- Самая слабая блокировка
- Автоматически берется при обычных SELECT
## ROW SHARE
```sql
LOCK TABLE table_name IN ROW SHARE MODE;
```
- Автоматически берется при SELECT FOR UPDATE/SHARE
## ROW EXCLUSIVE
```sql
LOCK TABLE table_name IN ROW EXCLUSIVE MODE;
```
- Автоматически берется при UPDATE, DELETE, INSERT
## SHARE
```sql
LOCK TABLE table_name IN SHARE MODE;
```
- Разрешает параллельное чтение
- Запрещает изменения данных
## SHARE ROW EXCLUSIVE
```sql
LOCK TABLE table_name IN SHARE ROW EXCLUSIVE MODE;
```
- Более строгая версия SHARE
- Запрещает параллельные SHARE блокировки
## EXCLUSIVE
```sql
LOCK TABLE table_name IN EXCLUSIVE MODE;
```
- Разрешает только ACCESS SHARE параллельно
- Запрещает все другие операции
## ACCESS EXCLUSIVE
```sql
LOCK TABLE table_name IN ACCESS EXCLUSIVE MODE;
```
- Самая строгая блокировка
- Запрещает любые параллельные операции
- Используется при ALTER TABLE, DROP TABLE и т.д.
# Параметры блокировок
## NOWAIT
```sql
SELECT * FROM table WHERE id = 1 FOR UPDATE NOWAIT;
```
- Если строка уже заблокирована - сразу ошибка вместо ожидания
## SKIP LOCKED
```sql
SELECT * FROM table FOR UPDATE SKIP LOCKED;
```
- Пропускает заблокированные строки
- Полезно для реализации очередей
# Конфликты и deadlocks
PostgreSQL автоматически обнаруживает взаимные блокировки (deadlocks) и откатывает одну из транзакций с ошибкой:
```
ERROR: deadlock detected
DETAIL: Process 123 waits for ShareLock on transaction 456; blocked by process 789.
```
# Мониторинг блокировок
Чтобы посмотреть текущие блокировки:
```sql
SELECT * FROM pg_locks;
SELECT blocked_locks.pid AS blocked_pid,
       blocking_locks.pid AS blocking_pid
FROM pg_catalog.pg_locks blocked_locks
JOIN pg_catalog.pg_locks blocking_locks
  ON blocking_locks.locktype = blocked_locks.locktype
  AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
  AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
  AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
  AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
  AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
  AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
  AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
  AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
  AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
  AND blocking_locks.pid != blocked_locks.pid
WHERE NOT blocked_locks.GRANTED;
```
# Когда использовать:
- **FOR UPDATE** - когда нужно изменить данные и предотвратить конкурентные изменения
- **FOR SHARE** - когда нужно прочитать данные и гарантировать их неизменность
- **Табличные блокировки** - для DDL операций или массовых изменений
- **NOWAIT/SKIP LOCKED** - для реализации очередей обработки

Выбор конкретного типа блокировки зависит от требований к параллелизму и согласованности данных.