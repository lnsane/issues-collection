
# Gitlab Runner 构建失败

## 异常原因
```shell
fatal: reference is not a tree:
```

## 解决方案：

```shell
# gitlab 配置文件 
 vim /etc/gitlab/gitlab.rb
```

```shell
# 修改配置文件的url
external_url 'http://119.23.247.159/'
```

```shell
# 修改的端口号
vim /opt/gitlab/embedded/conf/nginx.conf
```

```shell
#gzip  on;

    server {
        listen       80;
```


