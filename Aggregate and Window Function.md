
# Aggregate Function Advanced
* See example here.
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
Achieved by:
```sql
  SELECT 
    "class", 
    count(*), 
    count(*) 
    Filter (WHERE grade > 80), string_agg("name", ', ') 
  FROM zzz_tmp.classes c 
  GROUP BY "class" 
```
