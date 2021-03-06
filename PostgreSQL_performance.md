# Case when then 用于 group by
Calls table (leetcode) 1699:
```
+---------+-------+----------+
| from_id | to_id | duration |
+---------+-------+----------+
| 1       | 2     | 59       |
| 2       | 1     | 11       |
| 1       | 3     | 20       |
| 3       | 4     | 100      |
| 3       | 4     | 200      |
| 3       | 4     | 200      |
| 4       | 3     | 499      |
+---------+-------+----------+
```
```sql
  EXPLAIN SELECT 
    person1, 
    person2, 
    count(*) AS call_count, 
    SUM(duration) AS total_duration 
  FROM (
    SELECT 
      CASE 
        WHEN from_id > to_id 
          THEN to_id 
        ELSE from_id 
      END AS person1, 
      CASE 
        WHEN from_id > to_id 
          THEN from_id 
        ELSE to_id 
      END AS person2, 
      duration 
    FROM zzz_tmp.Calls
) AS tmp GROUP BY person1, person2
```
Result:
```
  HashAggregate  (cost=61.00..64.06 rows=204 width=24)
    Group Key: CASE WHEN (calls.from_id > calls.to_id) THEN calls.to_id ELSE calls.from_id END, CASE WHEN (calls.from_id > calls.to_id) THEN calls.from_id ELSE calls.to_id END
    ->  Seq Scan on calls  (cost=0.00..40.60 rows=2040 width=12)
```

#
PostgreSQL has two aggregate algorithms: HashAggregate and GroupAggregate. Hash using has table while group using sort.

# UNION
```
+---------+-------+----------+
| from_id | to_id | duration |
+---------+-------+----------+
| 1       | 2     | 59       |
| 2       | 1     | 11       |
| 1       | 3     | 20       |
| 3       | 4     | 100      |
| 3       | 4     | 200      |
| 3       | 4     | 200      |
| 4       | 3     | 499      |
+---------+-------+----------+
```
```sql
EXPLAIN SELECT person1, person2, count(*) as call_count, sum(duration) AS total_duration 
  FROM (
    SELECT from_id AS person1, to_id AS person2, duration FROM zzz_tmp.Calls WHERE from_id < to_id
    UNION ALL
    SELECT to_id AS person1, from_id AS person2, duration FROM zzz_tmp.Calls WHERE to_id < from_id
) AS tmp 
GROUP BY person1, person2
```
Result:
```
HashAggregate  (cost=98.20..100.20 rows=200 width=24)
  Group Key: calls.from_id, calls.to_id
  ->  Append  (cost=0.00..84.60 rows=1360 width=12)
        ->  Seq Scan on calls  (cost=0.00..35.50 rows=680 width=12)
              Filter: (from_id < to_id)
        ->  Seq Scan on calls calls_1  (cost=0.00..35.50 rows=680 width=12)
              Filter: (to_id < from_id)
```

# Between and VS multiple where
```sql
EXPLAIN SELECT * FROM zzz_tmp.classes c WHERE grade < 85 AND grade > 80

EXPLAIN SELECT * FROM zzz_tmp.classes c WHERE grade BETWEEN 80 AND 85
```
Results are the same:
```
Seq Scan on classes c  (cost=0.00..17.65 rows=3 width=132)
  Filter: ((grade >= '80'::numeric) AND (grade <= '85'::numeric))
```
# Subquery in select
```
+------------+------------+-------------+
| sale_date  | fruit      | sold_num    |
+------------+------------+-------------+
| 2020-05-01 | apples     | 10          |
| 2020-05-01 | oranges    | 8           |
| 2020-05-02 | apples     | 15          |
| 2020-05-02 | oranges    | 15          |
| 2020-05-03 | apples     | 20          |
| 2020-05-03 | oranges    | 0           |
| 2020-05-04 | apples     | 15          |
| 2020-05-04 | oranges    | 16          |
+------------+------------+-------------+
```
```sql
EXPLAIN select sale_date, sold_num - (
    SELECT sold_num 
    FROM zzz.Sales 
    WHERE sale_date = s2.sale_date AND fruit = 'oranges'
) AS diff 
FROM zzz.sales s2 WHERE fruit = 'apples'
ORDER BY sale_date
```
Result: 
```
Sort  (cost=111.68..111.69 rows=4 width=36)
  Sort Key: s2.sale_date
  ->  Seq Scan on sales s2  (cost=0.00..111.64 rows=4 width=36)
        Filter: (fruit = 'apples'::text)
        SubPlan 1
          ->  Seq Scan on sales  (cost=0.00..22.75 rows=1 width=4)
                Filter: ((sale_date = s2.sale_date) AND (fruit = 'oranges'::text))
```

# Where In
```sql
EXPLAIN SELECT "name" FROM zzz.classes c 
WHERE team NOT IN (
  SELECT team 
  FROM zzz.classes c2 
  WHERE grade < 80
)
```
Result:
```
Seq Scan on classes c  (cost=16.80..33.17 rows=255 width=32)
  Filter: (NOT (hashed SubPlan 1))
  SubPlan 1
    ->  Seq Scan on classes c2  (cost=0.00..16.38 rows=170 width=32)
          Filter: (grade < '80'::numeric)
```
<b>We can see that the 'IN' values are hashed as filter for table scan.</b>
