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

# CROSS JOIN 
* If a table A has 8 rows, then A cross join A will leads to 64 rows.
```
SELECT * FROM zzz_tmp.classes c CROSS JOIN zzz_tmp.classes c2;
SELECT * FROM zzz_tmp.classes c3, zzz_tmp.classes c4;
```

# Window function
window functions returns result within group partitioned by PARTITION clause. 

* rank() function returns in-partition rank of each partition. The following SQL is finding grade rank within each class.
```sql
    SELECT *,
       rank() OVER (PARTITION BY class
                     ORDER BY grade DESC) AS ranking
    FROM classes
```
