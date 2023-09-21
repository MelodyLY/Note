# flink作业相关

1. group offset代表从上次消费的位置继续消费
2. 不需要状态保存时，如简单的同步一些数据，可设置taskmanager.memory.managed.size=0，可避免浪费内存；另外可设置-XX:+UseG1GC

根据窗口数据划分的不同，目前 Apache Flink 有如下 3 种 Bounded Window：

- Tumble，滚动窗口，窗口数据有固定的大小，窗口数据无叠加；
- Hop，滑动窗口，窗口数据有固定大小，并且有固定的窗口重建频率，窗口数据有叠加；
- Session，会话窗口，窗口数据没有固定的大小，根据窗口数据活跃程度划分窗口，窗口数据无叠加。

Tumble 滚动窗口对应的语法如下：

```sql
SELECT
    [gk],
    [TUMBLE_START(timeCol, size)],
    [TUMBLE_END(timeCol, size)],
    agg1(col1),
    ...
    aggn(colN)
FROM Tab1
GROUP BY [gk], TUMBLE(timeCol, size)
```

其中：

- [gk] 决定了是否需要按照字段进行聚合；
- TUMBLE_START 代表窗口开始时间；
- TUMBLE_END 代表窗口结束时间；
- timeCol 是流表中表示时间字段；
- size 表示窗口的大小，如秒、分钟、小时、天。

举个例子，假如我们要计算每个人每天的订单量，按照 user 进行聚合分组：

```sql
SELECT 
    user,
    TUMBLE_START(rowtime, INTERVAL '1' DAY) as wStart,
    SUM(amount) 
FROM 
    Orders 
GROUP BY 
    TUMBLE(rowtime, INTERVAL '1' DAY),
    user;
```

Hop 滑动窗口对应语法如下：

```sql
SELECT
    [gk],
    [HOP_START(timeCol, slide, size)] , 
    [HOP_END(timeCol, slide, size)],
    agg1(col1),
    ...
    aggN(colN)
FROM 
    Tab1
GROUP BY 
    [gk], 
    HOP(timeCol, slide, size)
```

每次字段的意思和 Tumble 窗口类似：

- [gk] 决定了是否需要按照字段进行聚合；
- HOP_START 表示窗口开始时间；
- HOP_END 表示窗口结束时间；
- timeCol 表示流表中表示时间字段；
- slide 表示每次窗口滑动的大小；
- size 表示整个窗口的大小，如秒、分钟、小时、天。

举例说明，我们要每过一小时计算一次过去 24 小时内每个商品的销量：

```sql
SELECT 
    product,
    SUM(amount)
FROM 
    Orders 
GROUP BY 
    HOP(rowtime, INTERVAL '1' HOUR, INTERVAL '1' DAY),
    product
```

hive建表相关设置：

```sql
use db_name;
CREATE TABLE table_name ( 
    meta struct<id:string,sendTime:long,receiveTime:long,sender:string,resendCount:int,routingKeys:array<string>> COMMENT '字段描述', 
    user struct<id:string,token:string,login:boolean,loginName:string,grade:int,corp:string> COMMENT '字段描述', 
    fieldname STRING COMMENT '字段描述') 
COMMENT 'table描述' 
PARTITIONED BY(d STRING COMMENT 'date')  --分区字段
STORED AS ORC 
TBLPROPERTIES (  --除分区配置可以自己选择，其他三个配置最好都带上
    'partition.time-extractor.timestamp-pattern'='$d $h:00:00',  --如果不是分区表，该配置可以不需要
    'sink.partition-commit.policy.kind'='metastore,success-file', 
    'sink.partition-commit.success-file.name'='_MY_SUCCESS' ,
    'auto-compaction'='true' --有时候考虑写入性能可设置为false
 );
```

