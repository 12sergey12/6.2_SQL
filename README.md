# Домашнее задание к занятию 2. «SQL»

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/blob/virt-11/additional/README.md).

---

### Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose-манифест.

docker-compose up -d

export PGPASSWORD=netology && psql -h localhost -U test-admin-user test_db

sudo docker exec -it psql bash

root@799fc79e1efc:/#

psql -d test_db -U test-admin-user

psql (12.15 (Debian 12.15-1.pgdg120+1))

Type "help" for help.

test_db=#


![monitoring](https://github.com/12sergey12/6.2_SQL/blob/main/images/z.1.png)

---


### Задача 2

В БД из задачи 1: 

- создайте пользователя test-admin-user и БД test_db;
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже);
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db;
- создайте пользователя test-simple-user;
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE этих таблиц БД test_db.

Таблица orders:

- id (serial primary key);
- наименование (string);
- цена (integer).

Таблица clients:

- id (serial primary key);
- фамилия (string);
- страна проживания (string, index);
- заказ (foreign key orders).

Приведите:

- итоговый список БД после выполнения пунктов выше;
- описание таблиц (describe);
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db;
- список пользователей с правами над таблицами test_db.

test_db=# CREATE USER test_admin_user;

CREATE ROLE

test_db=# CREATE TABLE orders

(
   id SERIAL PRIMARY KEY,

   наименование TEXT,

   цена INTEGER
);

CREATE TABLE

test_db=# CREATE TABLE clients

(
	id SERIAL PRIMARY KEY,

	фамилия TEXT,

	"страна проживания" TEXT,

	заказ INTEGER,

	FOREIGN KEY (заказ) REFERENCES orders(id)

);

CREATE TABLE

test_db=# CREATE INDEX country_index ON clients ("страна проживания");

CREATE INDEX

test_db=# GRANT ALL ON TABLE orders TO test_admin_user;

GRANT

test_db=# GRANT ALL ON TABLE clients TO test_admin_user;

GRANT

test_db=# CREATE USER test_simple_user;

CREATE ROLE

test_db=# GRANT SELECT,INSERT,UPDATE,DELETE ON TABLE orders TO test_simple_user;

GRANT

test_db=# GRANT SELECT,INSERT,UPDATE,DELETE ON TABLE clients TO test_simple_user;

GRANT

test_db=# \l+


![monitoring](https://github.com/12sergey12/6.2_SQL/blob/main/images/z.2(d%2B).png)

![monitoring](https://github.com/12sergey12/6.2_SQL/blob/main/images/z.2(n).png)

![monitoring](https://github.com/12sergey12/6.2_SQL/blob/main/images/z.2(spisok).png)

![monitoring](https://github.com/12sergey12/6.2_SQL/blob/main/images/z.2.png)

---


### Задача 3

Используя SQL-синтаксис, наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL-синтаксис:
- вычислите количество записей для каждой таблицы.

Приведите в ответе:

    - запросы,
    - результаты их выполнения.

test_db=# INSERT INTO orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);

INSERT 0 5

test_db=# INSERT INTO clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');

INSERT 0 5

![monitoring](https://github.com/12sergey12/6.2_SQL/blob/main/images/z.3.png)

---


### Задача 4


Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys, свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения этих операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод этого запроса.
 
Подсказка: используйте директиву `UPDATE`.

![monitoring](https://github.com/12sergey12/6.2_SQL/blob/main/images/z.4.png)

---


### Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните, что значат полученные значения.

![monitoring](https://github.com/12sergey12/6.2_SQL/blob/main/images/z.5.png)

Чтение данных из таблицы clients происходит с использованием метода Seq Scan - последовательного чтения данных. Значение 0.00 - ожидаемые затраты на получение первой строки. Второе - 18.10 - ожидаемые затраты на получение всех строк. rows - ожидаемое число строк, которое должен вывести этот узел плана. При этом так же предполагается, что узел выполняется до конца. width - ожидаемый средний размер строк, выводимых этим узлом плана (в байтах). Каждая запись сравнивается с условием "заказ" IS NOT NULL. Если условие выполняется, запись вводится в результат. Если нет - отбрасывается.


---


### Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).

Остановите контейнер с PostgreSQL, но не удаляйте volumes.

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 


export PGPASSWORD=netology && pg_dumpall -h localhost -U test-admin-user > /media/postgresql/backup/all_$(date --iso-8601=m | sed 's/://g; s/+/z/g').sql

root@baranovsa:/home/baranovsa# docker-compose stop

Stopping psql ... done

root@baranovsa:/home/baranovsa# docker ps -a

root@baranovsa:/home/baranovsa# docker run --rm -d -e POSTGRES_USER=test-admin-user -e POSTGRES_PASSWORD=netology -e POSTGRES_DB=test_db -v psql_backup:/media/postgresql/backup --name psql2 postgres:12

b9f383df08de14f9d80d170bce05efcee87f29331824626e8143faa6cec3949c

root@baranovsa:/home/baranovsa# docker exec -it psql2  bash

root@b9f383df08de:/#

root@b9f383df08de:/media/postgresql/backup# pg_dump -U test-admin-user test_db > /media/postgresql/backup/test_db.backup

root@b9f383df08de:/media/postgresql/backup# ls

test_db.backup

root@b9f383df08de:/media/postgresql/backup# psql -U test-admin-user -d test_db -f /media/postgresql/backup//test_db.backup

SET

SET

SET

SET

SET

 set_config

------------
 
(1 row)

SET

SET

SET

SET

root@b9f383df08de:/# psql -h localhost -U test-admin-user test_db

psql (12.15 (Debian 12.15-1.pgdg120+1))

Type "help" for help.

test_db=# \l

![monitoring](https://github.com/12sergey12/6.2_SQL/blob/main/images/z.6.png)

![monitoring](https://github.com/12sergey12/6.2_SQL/blob/main/images/z.6.1.png)

---


---

