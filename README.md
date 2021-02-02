**Pivot** (Oracle)

Selecting all except specified columns

```sql
with
employee(id, firstname, lastname, hobbies) as
(
  select 1, 'a', 'b', '1' from dual union 
  select 2, 'a', 'b', '2' from dual union 
  select 3, 'a', 'b', '3' from dual union 
  select 4, 'c', 'd', '3' from dual union 
  select 5, 'e', 'f', '2' from dual  
)

select * 
from employee 
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
  max(id) foo,
  max(1)  bar
  for (hobbies) 
  IN ('2' as two, '3' as three)
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
