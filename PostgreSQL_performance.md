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
