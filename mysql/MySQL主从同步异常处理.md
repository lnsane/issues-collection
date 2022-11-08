# MySQL主从同步异常处理

先让从库停止同步
```SQL
STOP SLAVE
```

然后查看主库的状态

```
mysql> show master status;
+------------------+----------+--------------+--------------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB         | Executed_Gtid_Set |
+------------------+----------+--------------+--------------------------+-------------------+
| mysql-bin.000010 |   355989 | paas_record  | information_schema,mysql |                   |
+------------------+----------+--------------+--------------------------+-------------------+
1 row in set (0.01 sec)
```

然后拿File 和 Position 在从库执行在从库执行

```sql
change master to master_host='xxx.xxx.xxx.xxx', master_user='xxxxx',
master_password='xxxxx',
master_port=3306,
master_log_file='mysql-bin.000011',
master_log_pos=58834974;
```

然后在从库执行

```SQL
start slave;
```
