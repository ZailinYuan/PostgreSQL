
# Aggregate Function Advanced
1. <b>Filter clause</b>
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
    string_agg("name", ', ') 
  FROM zzz_tmp.classes c 
  GROUP BY "class" 
```
Then:
```
|class       |count|good|average|string_agg          |
|------------|-----|----|-------|--------------------|
|Inspect     |1    |0   |[NULL] |Bob                 |
|Math        |3    |2   |85.5   |Frank, Lily, Eva    |
|Team_Builder|4    |2   |87.5   |AJ, Bill, Toma, Rick|
```


2. WITHIN GROUP
* The WITHIN GROUP clause is particularly useful when performing aggregations on ordered subsets of data.
* Usually used with order by
* https://www.2ndquadrant.com/en/blog/the-within-group-and-filter-sql-clauses-of-postgresql-9-4/#:~:text=By%20using%20the%20WITHIN%20GROUP,certain%20conditions%2C%20thereby%20avoiding%20aggregations.
```
select 
     sell_date,
     count(1) as num_sold,
     string_agg(product,',') within group (order by product) as products
from t
group by sell_date
order by sell_date
```
