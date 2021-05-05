# logstash 配置学习笔记

```shell
# 获取文件路径

#比如 "/logs/base-server/base-server_info.log"


filter {
  grok {
    match => ["path","%{GREEDYDATA}/%{GREEDYDATA:filename}\.log"]
  }
}

# 结果
{
  "GREEDYDATA": [
    [
      "/logs/base-server"
    ]
  ],
  "filename": [
    [
      "base-server_info"
    ]
  ]
}


# 获取文件路径

#比如 "/logs/base-server/base-server_info.log"


filter {
  grok {
    match => {
      path => "/%{GREEDYDATA}/%{GREEDYDATA}/%{GREEDYDATA:filename}\.log"
    }
  }
}

# 结果
{
  "GREEDYDATA": [
    [
      "logs",
      "base-server"
    ]
  ],
  "filename": [
    [
      "base-server_info"
    ]
  ]
}

```

```shell

# 解析日志

# 2021-04-29 03:33:06,379 -DEBUG 13687  [nio-8077-exec-2] essageConverterMethodProcessor       AbstractMessageConverterMethodProcessor.java:  265 -> Using 'application/json', given [application/json, text/plain, */*] and supported [application/json]

#匹配规则
filter {
      grok {
        match => { "message" => "(?<date>\d+\-\d+\-\d+ \d+\:\d+\:\d+\,\d+) \-(?<level>\w+)" }
      }
    
#结果

{
  "date": [
    [
      "2021-04-29 03:33:06,379"
    ]
  ],
  "level": [
    [
      "DEBUG"
    ]
  ]
}

```

```shell
filter {
  grok {
    match => {
      "[log][file][path]" => "/%{GREEDYDATA}/%{GREEDYDATA}/%{GREEDYDATA:filename}\.log"
    }
    match => {message => "(?<date>\d+\-\d+\-\d+ \d+\:\d+\:\d+\,\d+) \-(?<level>\w+)" }
  }
}

filter {
  grok {
    match => {
      "path" => "/%{GREEDYDATA}/%{GREEDYDATA}/%{GREEDYDATA:filename}\.log"
    }
    match => {message => "(?<date>\d+\-\d+\-\d+ \d+\:\d+\:\d+\,\d+) \-(?<level>\w+)" }
    break_on_match => false
  }
}



```