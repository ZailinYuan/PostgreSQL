
# 子查询分类
### 按查询结果分类
1. 标量子查询 --- 查询结果为一行一列，理解为一个一行一列的 matrix
2. 行子查询 --- 查询结果为一行，理解为一个行向量
3. 列子查询 --- 查询结果为一列，理解为一个列向量
4. 表子查询 --- 查询结果为一个表，理解为矩阵
### 按查询出现在 SQL 语句中的位置查询
1. SELECT 中:
>> 仅支持标量子查询，相当于 SELECT 中的常量
2. FROM 后面:
>>  支持表子查询，FROM 后面当然接一张表
3. WHERE, HAVING:
>> 1. 标量子查询
>> 2. 行子查询
>> 3. 列子查询
4. EXIST 后面：
>> 表子查询

# 查询案例
```
|id |name |class       |grade|
|---|-----|------------|-----|
|1  |AJ   |Team_Builder|74   |
|2  |Bill |Team_Builder|79   |
|3  |Toma |Team_Builder|86   |
|4  |Frank|Math        |81   |
|5  |Lily |Math        |90   |
|6  |Bob  |Inspect     |78   |
|7  |Eva  |Math        |77   |
|8  |Rick |Team_Builder|89   |

```
1. WHERE + 标量子查询
```sql
  SELECT * FROM zzz_tmp.classes c 
  WHERE grade > (
    SELECT grade FROM zzz_tmp.classes c2 WHERE name = 'Bill'
  )
```
如果返回多行，报错：
```sql
  SELECT * FROM zzz_tmp.classes c 
  WHERE grade > (
    SELECT grade FROM zzz_tmp.classes c2 WHERE "class" = 'Math'
  )
```
ERROR: more than one row returned by a subquery used as an expression
4. 
