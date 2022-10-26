[TOC]

# 分组取出组内排名topk的数据

```sql
select * from
(select col1,col2,col3 row_number() over(partition by col2 order by col3 desc) as rank from table) a
where a.rank=1
```

# 行转列

```sql
select col1,col2
,concat_ws(',',collect_list(col3)) as col3
from table
group by col1,col2
```

# 列转行

```sql
-- 普通数组
select col1,col2,explode(split(col3)) as col3
from table
-- json array
select col1,col2,explode(json_array(col3)) as col3
from table --之后对col3进行json解析
```

# json解析

```sql
-- get_json_object
select get_json_object(col1,'$.key1') as f1,get_json_object(col1,'$.key2') as f2
from table
-- json_tuple
select a.* from table lateral view json_tuple(col1,'key1','key2') a as f1,f2
-- 列转行+json解析
-- 方式1
select col1, get_json_object(a.f,'$.key1') as f1 , get_json_object(a.f,'$.key2') as f2 from table lateral view explode(json_array(col3)) a as f
-- 方式2
select a.col1, get_json_object(a.f,'$.key1') as f1 , get_json_object(a.f,'$.key2') as f2
from 
(select col1,explode(json_array(col3)) as f) a
-- 方式3
select a.col1,b.f1,b.f2 from 
(select col1,explode(json_array(col3)) as f) a
lateral view json_tuple(a.f,'key1','key2') b as f1,f2
```

