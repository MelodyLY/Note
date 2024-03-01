[TOC]

# 后台执行模式

```shell
nohup COMMAND >./log.txt  2>&1 &
```

* nohup表示不挂断运行，能够保证终端挂断时命令不挂断（仅仅使用后台命令做不到）

* &表示后台运行

* 2>&1表示将错误和输出都输出到日志文件中（默认nohup.out)。对于& 1更准确的说应该是文件描述符1，而1标识标准输出，stdout；对于2 ，表示标准错误，stderr。

*   \>./log.txt表示就日志重定向到./log.txt文件中
  
  dsf


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

# git相关

## 设置非默认私钥

```sh
vim ~/.ssh/config
#填入以下内容
IdentityFile ~/.ssh/xxxx_ed25519#私钥文件地址
#可按上述格式依次新增多个私钥文件地址
```



# 用户权限类

## 用户和密码设置

```shell
# 创建用户
sudo adduser xxx
# 重置密码
sudo passwd xxx
# 删除用户
sudo userdel xxx
# 查看用户列表
sudo cat /etc/passwd
# 查看组
sudo groups xxx
# 更改组
sudo usermod -g root xxx # 更改组为root
# 设置sudo权限
sudo vim /etc/sudoers
```

sudo权限设置详细参考https://www.cnblogs.com/numa2022/articles/17874199.html

## 挂载目录

```shell
# 假设用户目录容量比较有限，可以在专门存放数据的目录比如/data下创建用户目录，然后在/home中建立软链接
# 如果/home已经有用户目录，可以先移动到/data中
sudo mv /home/luoyong /data/home
ln -s /data/home/luoyong /home/luoyong #建立软链接
```

# 并行调度

```shell
d=`date +%Y%m%d%H%M%S`
code=$RANDOM
echo $d
echo $code
sql_file=$1
start_date=$2
end_date=$3
# 分割字符串，获取文件名
file_name=`echo $sql_file | awk '{split($0,a,"."); print a[1]}'`
echo ${file_name}_${start_date}_${end_date}
# 日期遍历
while [[ "${start_date}" -le "${end_date}" ]];
do
    echo ${start_date}
    # 通过beeline执行脚本
    nohup beeline -u jdbc:hive2://xx.xx.xx.xx:20013/mgst_rec -n username -p password -f $sql_file --hivevar pday=$start_date > /home/luoyong/log/hive/hive_${file_name}_${start_date}_${2}_${3}_${d}_${code}.log 2>&1 &
    start_date=`date -d "${start_date} 1day" +%Y%m%d`
done
```

