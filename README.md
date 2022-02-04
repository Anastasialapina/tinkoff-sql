# Задание на знание SQL

### Таблица 1: Пилоты

| Ключ  | Наименование поля  | Комментарий |
| ------|--------------------| ------------|
| PK    | pilot_id           |             |
|       | name               |             |
|       | age                |             |
|       | rank               |             |
|       | education_level    |             |


### Таблица 2: Самолеты

| Ключ  | Наименование поля  | Комментарий |
| ------|--------------------| ------------|
| PK    | plane_id           |             |
|       | capacity           |Если cargo_flg = 1, то capacity – грузоподъемность (в тоннах), иначе количество пассажиров (в шт)|
|       | cargo_flg          |cargo_flg = 1 – грузовой самолет
cargo_flg = 0 – пассажирский|


### Таблица 3: Рейсы

| Ключ  | Наименование поля  | Комментарий |
| ------|--------------------| ------------|
| PK    | flight_id          |             |
| PK    | flight_dt          | Дата вылета |
| FK    | plane_id           |             |
| FK    | first_pilot_id     |             |
| FK    | second_pilot_id    |             |
|       | destination        | Аэропорт прилета|
|       | quantity           | Кол-во перевезенного груза или пассажиров (размерность совпадает с capacity)|


## Таблицы и запросы 
[https://www.db-fiddle.com/f/7SxAkKcrJYhVxeqmUq7Vps/12](https://www.db-fiddle.com/f/7SxAkKcrJYhVxeqmUq7Vps/13)

## Создание таблиц (использована MySQL v5.7): 

### Таблица 1: Пилоты

```sql
create table Table1
(
  pilot_id int primary key,
  name varchar(30),
  age int,
  rank int,
  education_level int
);
```

### Таблица 2: Самолеты

```sql
create table Table2
(
  plane_id int primary key,
  capacity int,
  cargo_flg int
);
```

### Таблица 3: Рейсы

```sql
create table Table3
(
  flight_id int primary key,
  flight_dt date,
  plane_id int,
  first_pilot_id int,
  second_pilot_id int,
  destination varchar(20),
  quantity int,
  FOREIGN KEY (plane_id) REFERENCES Table2(plane_id),
  FOREIGN KEY (first_pilot_id) REFERENCES Table1(pilot_id),
  FOREIGN KEY (second_pilot_id) REFERENCES Table1(pilot_id)
);
```

## Добавим немного данных в таблицы:

### Таблица 1: Пилоты

```sql
INSERT INTO Table1 (pilot_id, name, age, rank, education_level) VALUES (1, 'Ivan', 30, 1, 1);
INSERT INTO Table1 (pilot_id, name, age, rank, education_level) VALUES (2, 'Gleb', 50, 2, 2);
INSERT INTO Table1 (pilot_id, name, age, rank, education_level) VALUES (3, 'Vasiliy', 46, 4, 4);
```

### Таблица 2: Самолеты

```sql
INSERT INTO Table2 (plane_id, capacity, cargo_flg) VALUES (1, 50, 0);
INSERT INTO Table2 (plane_id, capacity, cargo_flg) VALUES (2, 100, 1);
INSERT INTO Table2 (plane_id, capacity, cargo_flg) VALUES (3, 70, 1);
```

### Таблица 3: Рейсы

```sql
INSERT INTO Table3 (flight_id, flight_dt, plane_id, first_pilot_id, second_pilot_id, destination, quantity) 
VALUES (1, '2021-08-10', 2, 1, 2, 'Sheremetyevo', 100);

INSERT INTO Table3 (flight_id, flight_dt, plane_id, first_pilot_id, second_pilot_id, destination, quantity) 
VALUES (2, '2021-08-21', 3, 1, 2, 'Pulkovo', 100);

INSERT INTO Table3 (flight_id, flight_dt, plane_id, first_pilot_id, second_pilot_id, destination, quantity) 
VALUES (3, '2021-01-08', 1, 1, 2, 'Vnukovo', 70);
```

## Написание запросов:

### Задача 1. Напишите запрос, который выведет пилотов, которые в качестве второго пилота в августе этого года ездили в аэропорт Шереметьево.

```sql
select distinct second_pilot_id from Table3
where flight_dt like "2022-08-%%" and destination="Sheremetyevo";
```

### Результат:
![alt text](https://i.ibb.co/sjW64dM/1.png)

Анализ: Данный запрос сработал корректно, так как необходимо найти записи о перелетах в августе этого (2022) года. Так как задание выполнено в феврале 2022 года, то записей о перелетах в августе 2022 года по логике быть не должно, поэтому данные не найдены. Но если не придерживаться правил логики и добавить данные о перелете в августе данного года, то в результате запроса мы получим корректные данные.

### Задача 2. Выведите пилотов старше 45 лет, которые совершили больше 30 полетов на грузовых самолетах.

```sql
select * from (select Table1.name, count(*) as 'amount' from Table1
join Table3 on (Table1.pilot_id = Table3.first_pilot_id or Table1.pilot_id = Table3.second_pilot_id)
join Table2 on Table3.plane_id = Table2.plane_id
where Table1.age > 45 and cargo_flg = 1
group by Table1.pilot_id) as subtable where amount > 30;
```

### Результат:
![alt text](https://i.ibb.co/PxnHwhq/2.png)

Анализ: Запрос сработал верно, потому что в таблицах нет данных о пилотах, старше 45 лет,  которые совершили больше 30 полетов на грузовых самолетах, так как слишком мало данных в таблицах.

### Задача 3. Выведите ТОП 10 пилотов-капитанов (first_pilot_id), которые перевезли наибольшее число пассажиров в этом году.

```sql
Select first_pilot_id 
from Table3 
join Table2 on Table3.plane_id=Table2.plane_id
where Table2.cargo_flg=0
group by Table3.first_pilot_id
order by sum(Table2.capacity) desc 
limit 10;
```
### Результат:
![alt text](https://i.ibb.co/d6BXy1r/3.png)

Анализ: Данный запрос сработал корректно, выведен только 1 запрос, а не 10, так как найдена только 1 запись по подходящим условиям.
