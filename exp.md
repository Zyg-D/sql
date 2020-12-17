----------------------------------------------------------------------------
**Leaving only a row with max value in group**

Option 1. Good, but requires the column to max to actually have one max in every group. If there are several records in a group with identical max, only one will remain. **Best with timestamps**. 
```sql
WITH 
tbl_max_date AS 
  (SELECT *, 
          ROW_NUMBER() OVER (PARTITION BY id, question ORDER BY date DESC) AS rn
   FROM `tests2.o1.mc`)
SELECT *
FROM tbl_max_date
WHERE rn = 1
```

Option 2. Keeps identical max values in a group.
```sql
WITH
tbl_src AS 
  (SELECT * FROM `tests2.o1.mc`)
SELECT *
FROM
  (SELECT id, question, MAX(date) AS date -- the same field name
   FROM tbl_src
   GROUP BY id, question
   ORDER BY id, question)
  INNER JOIN tbl_src 
    USING (id, question, date)
```
