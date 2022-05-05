# Aggregate (group) Function Tips
### Tip 1
Aggregate Function are not necessarily works with <b>GROUP BY</b> clause. It still works when no GRUOP BY and the whole table will be seen as a group. A well-known example is COUNT().
```sql
  SELECT max(grade) AS avg_grade 
  FROM zzz_tmp.classes c2 
```
<b>Having</b> function still works in this case:
```sql
  SELECT max(grade) AS avg_grade 
  FROM zzz_tmp.classes c2 
  HAVING max(grade) > 95
```

# Aggregate Function Advanced
### 1. <b>Filter clause</b>
* See example here.
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
Run:
```sql
  SELECT 
    "class", 
    count(*), 
    count(*) FILTER (WHERE grade > 80) AS good, 
    avg(grade) FILTER (WHERE grade > 80) AS average, 
    string_agg("name", ', ' ORDER BY "name")
  FROM zzz_tmp.classes c 
  GROUP BY "class" 
```
Then:
```
|class       |count|good|average|string_agg          |
|------------|-----|----|-------|--------------------|
|Inspect     |1    |0   |[NULL] |Bob                 |
|Math        |3    |2   |85.5   |Eva, Frank, Lily    |
|Team_Builder|4    |2   |87.5   |AJ, Bill, Rick, Toma|
```

### 2. <b>GROUPING SETS, CUBE, and ROLLUP</b>
* To deal with more complex grouping oerations.<br>
See example here:
```
|id |name |class        |grade|team |
|---|-----|-------------|-----|-----|
|1  |AJ   |Team_Builder |74   |BA   |
|2  |Bill |Team_Builder |79   |DEV  |
|3  |Toma |Team_Builder |86   |DEV  |
|4  |Frank|Math         |81   |DEV  |
|5  |Lily |Math         |90   |DEV  |
|6  |Bob  |Inspect      |78   |DEV  |
|7  |Eva  |Math         |77   |BA   |
|8  |Rick |Team_Builder |89   |OTHER|
```
Now we wanna know max, min and avg grade group by class and team and total.
```sql
    SELECT "class", team, max(grade), min(grade), avg(grade) 
    FROM zzz_tmp.classes c 
    GROUP BY GROUPING SETS (("class"), (team), ())
```
Result:
```
|class        |team   |max|min|avg   |
|-------------|-------|---|---|------|
|[NULL]       |[NULL] |90 |74 |81.75 |
|Inspect      |[NULL] |78 |78 |78.00 |
|Math         |[NULL] |90 |77 |82.66 |
|Team_Builder |[NULL] |89 |74 |82.00 |
|[NULL]       |OTHER  |89 |89 |89.00 |
|[NULL]       |DEV    |90 |78 |82.80 |
|[NULL]       |BA     |77 |74 |75.50 |
```
Remind that "class" and "team" must be declared in GROUPING SETS, otherwise it's not a valid query. <br/>
Executor does two rounds of hash to aggregate these two groups.<br/>
* Having clause still works.
```sql
    SELECT "class", team, max(grade), min(grade), avg(grade) 
    FROM zzz_tmp.classes c 
    GROUP BY GROUPING SETS (("class"), (team), ())
    HAVING avg(grade) > 80
```
Result:
```
|class        |team   |max|min|avg   |
|-------------|-------|---|---|------|
|[NULL]       |[NULL] |90 |74 |81.75 |
|Math         |[NULL] |90 |77 |82.66 |
|Team_Builder |[NULL] |89 |74 |82.00 |
|[NULL]       |OTHER  |89 |89 |89.00 |
|[NULL]       |DEV    |90 |78 |82.80 |
```
* Works with <b>Filter</b> clause.
```sql
    SELECT "class", team, 
      max(grade) FILTER (WHERE grade > 80) AS "max", 
      min(grade) FILTER (WHERE grade > 80) AS "min", 
      avg(grade) FILTER (WHERE grade > 80) AS "avg"
    FROM zzz_tmp.classes c 
    GROUP BY GROUPING SETS (("class"), (team), ())
```

### 12. WITHIN GROUP
* The WITHIN GROUP clause is particularly useful when performing aggregations on ordered subsets of data.
* Usually used with order by
* https://www.postgresql.org/docs/current/functions-aggregate.html
* https://www.2ndquadrant.com/en/blog/the-within-group-and-filter-sql-clauses-of-postgresql-9-4/#:~:text=By%20using%20the%20WITHIN%20GROUP,certain%20conditions%2C%20thereby%20avoiding%20aggregations.
