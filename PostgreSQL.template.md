# 显示所有进程
```sql
SHOW processlist
```

# Common bugs
### source database "DB_TEMPLATE" is being accessed by other users
Solution:
```sql
SELECT pg_terminate_backend(pg_stat_activity.pid)
FROM pg_stat_activity
WHERE pg_stat_activity.datname = 'DMH_IM_DEV' -- change this to your DB
  AND pid <> pg_backend_pid();
```
pg_stat_activity is the table with all active processes of a DB
