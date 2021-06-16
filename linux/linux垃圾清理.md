# linux垃圾清理

查看linux根目录的文件大小
```shell
du -h --max-depth=1 /
```

Mysql 清除Binlog日志

查看主库和从库正在使用的binlog是哪个文件
```mysql
show master status;
show slave status;
```


删除指定日期以前的日志索引中binlog日志文件

```mysql
purge master logs before '2016-09-01 17:20:00';
```

删除指定日志文件的日志索引中binlog日志文件

```mysql
purge master logs to 'mysql-bin.000022'
```


设置binlog过期时间

```shell
mysql> show variables like 'expire_logs_days';
+------------------+-------+
| Variable_name  | Value |
+------------------+-------+
| expire_logs_days |   0  |
+------------------+-------+
mysql> set global expire_logs_days = 30;
```

设置binlog多少天过期


查看所有binlog

```shell
show binary logs;
```