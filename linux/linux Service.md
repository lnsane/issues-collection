```shell

[Unit]
# 详情
Description=Halo Service
# 文档地址
Documentation=https://halo.run
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=USER
# 启动命令
ExecStart=/root/nacos/bin/startup.sh
ExecReload=/bin/kill -s -HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
Restart=always
StandOutput=syslog
StandError=inherit

[Install]
WantedBy=multi-user.target

```