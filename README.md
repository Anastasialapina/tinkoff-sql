# Задание на знание SQL

### Таблица 1: Пилоты

| Ключ  | Наименование поля  | Комментарий |
| ------|:------------------:| -----------:|
| PK    | pilot_id           |             |
|       | name               |             |
|       | age                |             |
|       | rank               |             |
|       | education_level    |             |


### Таблица 2: Самолеты

| Ключ  | Наименование поля  | Комментарий |
| ------|:------------------:| -----------:|
| PK    | plane_id           |             |
|       | capacity           |Если cargo_flg = 1, то capacity – грузоподъемность (в тоннах), иначе количество пассажиров (в шт)|
|       | cargo_flg          |cargo_flg = 1 – грузовой самолет
cargo_flg = 0 – пассажирский|


### Таблица 3: Рейсы

| Ключ  | Наименование поля  | Комментарий |
| ------|:------------------:| -----------:|
| PK    | flight_id          |             |
| PK    | flight_dt          | Дата вылета |
| FK    | plane_id           |             |
| FK    | first_pilot_id     |             |
| FK    | second_pilot_id    |             |
|       | destination        | Аэропорт прилета|
|       | quantity           | Кол-во перевезенного груза или пассажиров (размерность совпадает с capacity)|

