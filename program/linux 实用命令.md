[TOC]

# 后台执行模式

```shell
nohup COMMAND >./log.txt  2>&1 &
```

*   nohup表示不挂断运行，能够保证终端挂断时命令不挂断（仅仅使用后台命令做不到）
*   &表示后台运行
*   2>&1表示将错误和输出都输出到日志文件中（默认nohup.out)。对于& 1更准确的说应该是文件描述符1，而1标识标准输出，stdout；对于2 ，表示标准错误，stderr。
*   \>./log.txt表示就日志重定向到./log.txt文件中

# hive拉取数据

```sql
start_date=$1
end_date=$2
hive -e """
set hive.cli.print.header=true;
set hive.resultset.use.unique.column.names=false;
select * from table where d between '${start_date}' and '${end_date}'
""" | sed 's/\t/,/g' > ./data/test_${start_date}_${end_date}.csv
```
$\alpha$

*   set hive.cli.print.header=true;表示显示列名
*   set hive.resultset.use.unique.column.names=false;表示不显示表名
*   |为管道命令，管道是一种通信机制，通常用于进程间的通信，它表现出来的形式为将前面每一个进程的输出（stdout）直接作为下一个进程的输入（stdin）
*   sed为流编辑器，其中s表示查找，g表示全部替换，即将所有制表符替换为逗号

# gpu状态查看

```shell
watch -n 10 nvidia-smi
#-n 10表示每10秒进行状态更新
```

# 统计类

## 统计文件行数

```shell
wc -l 文件名
```

## 统计文件夹大小

```shell
# 统计当前文件夹的大小
du -sh
# 统计当前目录下直属子目录和文件的大小
du -h --max-depth=0 *
```

## 统计硬盘大小

```shell
df -h
```

