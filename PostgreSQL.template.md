# 

# Common bugs
### source database "DB_TEMPLATE" is being accessed by other users
Solution:
```sql
SELECT *, pg_terminate_backend(procpid) 
FROM pg_stat_activity 
WHERE usename='username';
```
