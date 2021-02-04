**Example table (Oracle)**
```sql
WITH
x(id, yr, more_info) AS
    (SELECT 1, 2020, 'info1' FROM DUAL UNION 
     SELECT 2, 2021, 'info2' FROM DUAL UNION 
     SELECT 3, 2021, 'info3' FROM DUAL UNION 
     SELECT 4, 2020, 'info4' FROM DUAL ),
n(n_id, n_id2, n_year) AS
    (SELECT 1, 3, 2021 FROM DUAL UNION 
     SELECT 2, 4, 2021 FROM DUAL )
```

**Example table (Big Query)**
```sql
WITH 
Roster AS
 (SELECT 'Adams' as LastName, 50 as SchoolID UNION ALL
  SELECT 'Buchanan', 52 UNION ALL
  SELECT 'Coolidge', 52 UNION ALL
  SELECT 'Davis', 51 UNION ALL
  SELECT 'Eisenhower', 77)
SELECT * FROM Roster
```

**All accessible tables, views (Oracle)**

    select * from ALL_TAB_COLUMNS

**Pivot (Oracle)**

Selecting all except specified columns

```sql
with
employee ( id, firstname, lastname, hobbies ) as
( select    1,  'a',       'b',      '1'     from dual union 
  select    2,  'a',       'b',      '2'     from dual union 
  select    3,  'a',       'b',      '3'     from dual union 
  select    4,  'c',       'd',      '3'     from dual union 
  select    5,  'e',       'f',      '2'     from dual  
)
select * from employee 
pivot
( 
  max(1) -- fake  
  for (hobbies) -- put the undesired columns here
  IN () -- no values here...
) 
where 1=1 -- add filters here...
order by id
```
result:  
```
+----+-----------+----------+
| ID | FIRSTNAME | LASTNAME |
+====+===========+==========+
|  1 | a         | b        |
|  2 | a         | b        |
|  3 | a         | b        |
|  4 | c         | d        |
|  5 | e         | f        |
+----+-----------+----------+
```

Actual pivot example:
```sql
select * 
from employee 
pivot
(
  max(id) foo, -- calculation and new col sufix
  max(1)  bar -- calculation and new col sufix
  for (hobbies) -- old col to pivot
  IN ('2' as two, '3' as three) -- values from the old col as names for new cols
)
```
result:
```
+-----------+----------+---------+---------+-----------+-----------+
| FIRSTNAME | LASTNAME | TWO_FOO | TWO_BAR | THREE_FOO | THREE_BAR |
+===========+==========+=========+=========+===========+===========+
| c         | d        |       4 |       1 |         4 |         1 |
| e         | f        |       5 |       1 |    (null) |    (null) |
| a         | b        |       2 |       1 |         3 |         1 |
+-----------+----------+---------+---------+-----------+-----------+
```

-----------------------------------------------------------------------------

**Unpivot (Oracle)**

```sql
with
sale_stats ( id, year, A_qty, A_val, B_qty, B_val ) as
( select      1, 2018,   100,  1000,  2000,  4000   from dual union 
  select      2, 2019,   150,  1500,  2500,  5000   from dual   
)
SELECT * FROM sale_stats
UNPIVOT (
    (quantity, amount) -- new col names for old values
    FOR product_code -- name for new dimension col (old col names will go to this col as values)
    IN (
        (a_qty, a_val) AS 'A', -- old col names as new values in dimension col
        (b_qty, b_val) AS 'B' 
    )
);
```
result:
```
+----+------+--------------+----------+--------+
| ID | YEAR | PRODUCT_CODE | QUANTITY | AMOUNT |
+====+======+==============+==========+========+
|  1 | 2018 | A            |      100 |   1000 |
|  1 | 2018 | B            |     2000 |   4000 |
|  2 | 2019 | A            |      150 |   1500 |
|  2 | 2019 | B            |     2500 |   5000 |
+----+------+--------------+----------+--------+
```

--------------------------------------------------------------

