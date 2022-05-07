# Update rows by 'Join' two table
```sql
    UPDATE Sales_Import SI
    SET AccountNumber = RAN.AccountNumber
    FROM RetrieveAccountNumber RAN
    WHERE RAN.LeadID = SI.LeadID; 
```

# Update by case when then
```sql
    UPDATE salary SET sex = CASE 
        WHEN sex = 'm' THEN 'f' 
        WHEN sex = 'f' THEN 'm' 
        ELSE 'other' 
       END
```

# Distinct on 
* Return the first row from each group grouped by name. However, the first age is not guaranteed unless an order is given.
```sql
    SELECT DISTINCT ON (name) name, age FROM ui.prisoners
```
* This SQL return different result from the previous one since the minimum age of each group grouped by name will be returned.
```sql
    SELECT DISTINCT ON (name) name, age FROM ui.prisoners ORDER BY name, age  
```
* To distinct tuples, the following two are the same.
```sql
    SELECT "class", "name" FROM zzz_tmp.classes c2 GROUP BY "class", "name"; 
    SELECT DISTINCT "class", "name" FROM zzz_tmp.classes c3; 
```
>> Constant columns in SELECT list refering are not constraint by GROUP BY list
```sql
    SELECT "class", "name", 'Bill' AS ceo FROM zzz_tmp.classes c2 GROUP BY "class", "name"
```
* The SELECT list of DISTINCT ON clause is also constraint by GROUP BY lists.


# CROSS JOIN 
* If a table A has 8 rows, then A cross join A will leads to 64 rows.
```sql
SELECT * FROM zzz_tmp.classes c CROSS JOIN zzz_tmp.classes c2;
SELECT * FROM zzz_tmp.classes c3, zzz_tmp.classes c4;
```
* For <b>all join cases</b>, see offical document:<br />
https://www.postgresql.org/docs/10/queries-table-expressions.html#QUERIES-GROUP

# LATERAL
* Last subquery be able to refer outer query table.
```
|id |name |class       |grade|
|---|-----|------------|-----|
|1  |AJ   |Team_Builder|74   |
|2  |Bill |Team_Builder|79   |
|3  |Toma |Team_Builder|86   |
|4  |Frank|Math        |81   |
|5  |Lily |Math        |90   |
|6  |Bob  |Inspect     |78   |
|7  |Eva  |Math        |77   |
|8  |Rick |Team_Builder|89   |

```
If we do query like below:
```sql
SELECT DISTINCT c."class", avgg.avg_grade FROM zzz_tmp.classes c, 
    (
        SELECT avg(grade) AS avg_grade 
        FROM zzz_tmp.classes c2 
        WHERE c."class" = c2."class" 
        GROUP BY "class"
    ) AS avgg
```
An error raised saying <i>'There is an entry for table "c", but it cannot be referenced from this part of the query.'</i><br>
If we added <b>lateral</b> here:
```sql
SELECT DISTINCT c."class", avgg.avg_grade FROM zzz_tmp.classes c, 
    LATERAL (
        SELECT avg(grade) AS avg_grade 
        FROM zzz_tmp.classes c2 
        WHERE c."class" = c2."class" 
        GROUP BY "class"
    ) AS avgg
```
It works! Subquery can refer 'c' of outer query.

# Limit Offset
* What if limit or offset is out of boundary?
    Return empty result set.

# SELECT empty table
* What's the difference?
```sql
 SELECT value;
 SELECT * FROM table;
```
The first one has to return a value, at least a [NULL] returned had the result set is empty. The second returns a empty result set if not record meets. Thus, if you wanna get a [NULL] when nother there, use the first one, or when you need use functions like COALESCE or NULLIF, use the first one is a solution.

# COALESCE
```
select (coalesce(val1, val2, val3, null);
```
Return val1, if val1 is null return val2, if val2 is null return val3, if val3 is null return null.

# Window function
window functions returns result within group partitioned by PARTITION clause. 

* rank() function returns in-partition rank of each partition. The following SQL is finding grade rank within each class.
```sql
    SELECT *,
       rank() OVER (PARTITION BY class
                     ORDER BY grade DESC) AS ranking
    FROM classes
```
