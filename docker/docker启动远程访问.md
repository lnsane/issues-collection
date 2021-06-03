## docker启动远程访问

修改 docker 文件
```shell
vim /etc/docker/daemon.json
```

```json
{
  "hosts": [
    "tcp://0.0.0.0:2375",
    "unix:///var/run/docker.sock"
  ]
}

```


重启docker

```shell
systemctl restart docker
```



```shell

➜  ~ ss -ntlp | grep 2375
LISTEN     0      128       [::]:2375                  [::]:*                   users:(("dockerd-current",pid=19047,fd=5))

```