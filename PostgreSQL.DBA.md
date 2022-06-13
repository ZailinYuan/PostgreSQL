# 显示所有进程
```sql
SHOW processlist
```
# 查找所有 DB
```sql
SELECT datname FROM pg_database;
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

# 用户权限
查看用户所有权限
```sql
information_schema.role_table_grants
select * from information_schema.table_privileges where grantee='cc';
```
