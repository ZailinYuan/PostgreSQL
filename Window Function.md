# Window function and aggregation
* If the query contains any window functions, these functions are evaluated after any grouping, aggregation, and HAVING filtering is performed. That is, if the query uses any aggregates, GROUP BY, or HAVING, then the rows seen by the window functions are the group rows instead of the original table rows from FROM/WHERE.
* When multiple window functions are used, all the window functions having syntactically equivalent PARTITION BY and ORDER BY clauses in their window definitions are guaranteed to be evaluated in a single pass over the data. Therefore they will see the same sort ordering, even if the ORDER BY does not uniquely determine an ordering.

# LAG
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
```
Result:
```
```
