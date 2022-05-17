# Window function and aggregation
* If the query contains any window functions, these functions are evaluated after any grouping, aggregation, and HAVING filtering is performed. That is, if the query uses any aggregates, GROUP BY, or HAVING, then the rows seen by the window functions are the group rows instead of the original table rows from FROM/WHERE.
* When multiple window functions are used, all the window functions having syntactically equivalent PARTITION BY and ORDER BY clauses in their window definitions are guaranteed to be evaluated in a single pass over the data. Therefore they will see the same sort ordering, even if the ORDER BY does not uniquely determine an ordering.

# lag()
* LAG function is used to get previous row value.
```sql
  SELECT * FROM (
    SELECT 
      *, 
      lag(grade, 1) over(ORDER BY "name") AS prev,
      lag(grade, 2) over(ORDER BY "name") AS prev2
    FROM zzz_tmp.classes c
  ) AS tmp
```
Source table:
```
|id |name  |class       |grade|team |
|---|------|------------|-----|-----|
|1  |AJ    |Team_Builder|74   |BA   |
|2  |Bill  |Team_Builder|79   |DEV  |
|6  |Bob   |Inspect     |78   |DEV  |
|7  |Eva   |Math        |77   |BA   |
|4  |Frank |Math        |81   |DEV  |
|5  |Lily  |Math        |90   |DEV  |
|8  |Rick  |Team_Builder|89   |OTHER|
|3  |Toma  |Team_Builder|86   |DEV  |
```
Result:
```
|id |name |class       |grade|team |prev|prev2|
|---|-----|------------|-----|-----|----|-----|
|1  |AJ   |Team_Builder|74   |BA   |    |     |
|2  |Bill |Team_Builder|79   |DEV  |74  |     |
|6  |Bob  |Inspect     |78   |DEV  |79  |74   |
|7  |Eva  |Math        |77   |BA   |78  |79   |
|4  |Frank|Math        |81   |DEV  |77  |78   |
|5  |Lily |Math        |90   |DEV  |81  |77   |
|8  |Rick |Team_Builder|89   |OTHER|90  |81   |
|3  |Toma |Team_Builder|86   |DEV  |89  |90   |
```
<b>!!! lag offset number can be negative</b>. See below:
```
|id |name |class       |grade|team |prev|
|---|-----|------------|-----|-----|----|
|1  |AJ   |Team_Builder|74   |BA   |78  |
|2  |Bill |Team_Builder|79   |DEV  |77  |
|6  |Bob  |Inspect     |78   |DEV  |81  |
|7  |Eva  |Math        |77   |BA   |90  |
|4  |Frank|Math        |81   |DEV  |89  |
|5  |Lily |Math        |90   |DEV  |86  |
|8  |Rick |Team_Builder|89   |OTHER|    |
|3  |Toma |Team_Builder|86   |DEV  |    |
```
# first_value()
Attach first_value of the group to each row.
```sql
SELECT 
	*, 
	first_value(grade) over(PARTITION BY team ORDER BY grade desc) AS grade 
FROM zzz_tmp."classes"
```
```
|id |name |class       |grade|team |grade|
|---|-----|------------|-----|-----|-----|
|7  |Eva  |Math        |77   |BA   |77   |
|1  |AJ   |Team_Builder|74   |BA   |77   |
|5  |Lily |Math        |90   |DEV  |90   |
|3  |Toma |Team_Builder|86   |DEV  |90   |
|4  |Frank|Math        |81   |DEV  |90   |
|2  |Bill |Team_Builder|79   |DEV  |90   |
|6  |Bob  |Inspect     |78   |DEV  |90   |
|8  |Rick |Team_Builder|89   |OTHER|89   |
```

# rank() && dense_rank()
