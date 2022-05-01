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

# Window function
