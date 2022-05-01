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
