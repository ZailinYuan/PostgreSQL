# 
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
