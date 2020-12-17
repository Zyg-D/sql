----------------------------------------------------------------------------
**Leaving only a row with max value in group**

Option 1. Good, but requires the column to max to actually have one max in every group. If there are several records in a group with identical max, only one will remain. Good with timestamps. 
```sql
WITH tbl_max_date AS (
  SELECT *,
         ROW_NUMBER() OVER (PARTITION BY id, question ORDER BY date DESC) AS rn
  FROM `tests2.o1.mc`
)
SELECT *
FROM tbl_max_date
WHERE rn = 1
```

Option 2. Keeps identical max values in a group, but is a long code.
```sql
with 
tbl_src as (select * from `tests2.o1.mc` order by id, date),
tbl_max_date as (
  select
    id,
    question,
    MAX(date) as max_date
  from
    tbl_src
  group by
    id,
    question
)
select 
  tbl_src.*
from
  tbl_src
  inner join
    tbl_max_date
    on tbl_src.id  = tbl_max_date.id
    and tbl_src.question  = tbl_max_date.question
    and tbl_src.date  = tbl_max_date.max_date

```
