# 一次binlog的数据恢复

## what is binlog?

MySQL Binlog
介绍 [Binary Log Overview](https://dev.mysql.com/doc/internals/en/binary-log-overview.html#:~:text=The%20binary%20log%20is%20a,to%20a%20MySQL%20server%20instance.&text=It%20contains%20all%20statements%20that,row%2Dbased%20logging%20is%20used)

> The binary log is a set of log files that contain information about data modifications made to a MySQL server instance.

> It contains all statements that update data. It also contains statements that potentially could have updated it (for example, a DELETE which matched no rows), unless row-based logging is used

简而言之,binlog就是记录所有的更新操作的,比 **update** 或者**delete**这样的语句

## why using binlog?

我们在很多时候不小心删了一条数据或者改了一条数据的信息这个时候就需要binlog恢复

## how using binlog?

使用binlog那么在数据库中一定要开启binlog

```shell
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
server-id = 1
log-bin = mysql-bin
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
datadir = /var/lib/mysql
socket = /var/lib/mysql/mysql.sock

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links = 0
log-error = /var/log/mysqld.log
pid-file = /var/run/mysqld/mysqld.pid
```

```mysql
# 查看Binlog的状态
SHOW VARIABLES LIKE 'log_bin'
# mysql> show variables like 'log_bin';
# +---------------+-------+
# | Variable_name | Value |
# +---------------+-------+
# | log_bin       | ON    |
# +---------------+-------+
# 1 row in set (0.00 sec)
```

```mysql
# 查看当前的binlog文件序号
SHOW MASTER STATUS
# mysql> show master status;
# +------------------+-----------+--------------+------------------+-------------------+
# | File             | Position  | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
# +------------------+-----------+--------------+------------------+-------------------+
# | mysql-bin.000008 | 141606178 |              |                  |                   |
# +------------------+-----------+--------------+------------------+-------------------+
# 1 row in set (0.00 sec)
```

```mysql
# 查看binlog的详情信息
SHOW GLOBAL VARIABLES LIKE '%bin%'
# mysql> SHOW GLOBAL VARIABLES LIKE '%bin%';
# +--------------------------------------------+--------------------------------+
# | Variable_name                              | Value                          |
# +--------------------------------------------+--------------------------------+
# | bind_address                               | *                              |
# | binlog_cache_size                          | 32768                          |
# | binlog_checksum                            | CRC32                          |
# | binlog_direct_non_transactional_updates    | OFF                            |
# | binlog_error_action                        | ABORT_SERVER                   |
# | binlog_format                              | ROW                            |
# | binlog_group_commit_sync_delay             | 0                              |
# | binlog_group_commit_sync_no_delay_count    | 0                              |
# | binlog_gtid_simple_recovery                | ON                             |
# | binlog_max_flush_queue_time                | 0                              |
# | binlog_order_commits                       | ON                             |
# | binlog_row_image                           | FULL                           |
# | binlog_rows_query_log_events               | OFF                            |
# | binlog_stmt_cache_size                     | 32768                          |
# | binlog_transaction_dependency_history_size | 25000                          |
# | binlog_transaction_dependency_tracking     | COMMIT_ORDER                   |
# | innodb_api_enable_binlog                   | OFF                            |
# | innodb_locks_unsafe_for_binlog             | OFF                            |
# | log_bin                                    | ON                             |
# | log_bin_basename                           | /var/lib/mysql/mysql-bin       |
# | log_bin_index                              | /var/lib/mysql/mysql-bin.index |
# | log_bin_trust_function_creators            | OFF                            |
# | log_bin_use_v1_row_events                  | OFF                            |
# | log_statements_unsafe_for_binlog           | ON                             |
# | max_binlog_cache_size                      | 18446744073709547520           |
# | max_binlog_size                            | 1073741824                     |
# | max_binlog_stmt_cache_size                 | 18446744073709547520           |
# | sync_binlog                                | 1                              |
# +--------------------------------------------+--------------------------------+
# 28 rows in set (0.00 sec)
# 
```

```mysql
# 查看binlog 文件列表和大小 单位 字节
SHOW BINARY LOGS;
# mysql> SHOW BINARY LOGS;
# +------------------+-----------+
# | Log_name         | File_size |
# +------------------+-----------+
# | mysql-bin.000001 |       177 |
# | mysql-bin.000002 |       177 |
# | mysql-bin.000003 |       177 |
# | mysql-bin.000004 |     45267 |
# | mysql-bin.000005 |    233088 |
# | mysql-bin.000006 |     15053 |
# | mysql-bin.000007 |      1281 |
# | mysql-bin.000008 | 141606178 |
# +------------------+-----------+
# 8 rows in set (0.01 sec)

```

```shell
# 将 mysql-bin.000001 输出到back2001.sql 会进行base64解码
mysqlbinlog  --base64-output=DECODE-ROWS /root/mysqlbin2/mysql-bin.000001 > back2001.sql
```

--base64-output==DECODE-ROWS

```shell
      Determine when the output statements should be
      base64-encoded BINLOG statements: 'never' disables it and
      works only for binlogs without row-based events;
      'decode-rows' decodes row events into commented
      pseudo-SQL statements if the --verbose option is also
      given; 'auto' prints base64 only when necessary (i.e.,
      for row-based events and format description events).  If
      no --base64-output[=name] option is given at all, the
      default is 'auto'.
```

```shell
# 将 mysql-bin.000002 文件从 4 的固定点开始恢复 指定的数据库
mysqlbinlog  --start-position=4 --end-position=1640526 /root/mysqlbin2/mysql-bin.000002 | mysql -u root -p onestop-test
```

--start-position

```shell
 Start reading the binlog at position N. Applies to the
 first binlog passed on the command line.
```