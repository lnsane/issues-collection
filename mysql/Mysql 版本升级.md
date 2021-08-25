# Mysql 版本升级

## 为什么要版本升级

为了和线上的版本数据库一直

## 从MySQL 5.6 升级到 8.0.11

升级依赖

```shell
yum install mysql-community-*8.0.11*
```

## 更新数据库错误信息

```shell
 mysql_upgrade -u root -p 123456
```

```shell
[root@10-23-208-214 yum.repos.d]# mysql_upgrade -u root -p
Enter password:
Checking if update is needed.
Checking server version.
Running queries to upgrade MySQL server.
Upgrading system table data.
Checking system database.
mysql.columns_priv                                 OK
mysql.component                                    OK
mysql.db                                           OK
mysql.default_roles                                OK
mysql.engine_cost                                  OK
mysql.func                                         OK
mysql.general_log                                  OK
mysql.global_grants                                OK
mysql.gtid_executed                                OK
mysql.help_category                                OK
mysql.help_keyword                                 OK
mysql.help_relation                                OK
mysql.help_topic                                   OK
mysql.innodb_index_stats                           OK
mysql.innodb_table_stats                           OK
mysql.ndb_binlog_index                             OK
mysql.password_history                             OK
mysql.plugin                                       OK
mysql.procs_priv                                   OK
mysql.proxies_priv                                 OK
mysql.role_edges                                   OK
mysql.server_cost                                  OK
mysql.servers                                      OK
mysql.slave_master_info                            OK
mysql.slave_relay_log_info                         OK
mysql.slave_worker_info                            OK
mysql.slow_log                                     OK
mysql.tables_priv                                  OK
mysql.time_zone                                    OK
mysql.time_zone_leap_second                        OK
mysql.time_zone_name                               OK
mysql.time_zone_transition                         OK
mysql.time_zone_transition_type                    OK
mysql.user                                         OK
Found outdated sys schema version 1.5.2.
Upgrading the sys schema.
Checking databases.
sys.sys_config                                     OK
Upgrade process completed successfully.
Checking if update is needed
```
更新完毕