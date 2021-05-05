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

filter grok 调试 https://grokdebug.herokuapp.com/

```logstash
input {
    beats {
        port => 5044
        type => "erp"
    }
    beats {
        port => 5043
        type => "mysql"
    }
}
filter {
    if[type] == "erp" {
        grok {
            match => {
                "[log][file][path]" => "%{GREEDYDATA}/%{GREEDYDATA:filename}\.log"
            }
            match => {"message" => "(?<date>\d+\-\d+\-\d+ \d+\:\d+\:\d+\,\d+).*.+(?=[A-z])(?<level>\DEBUG|INFO|WARRING|ERROR).*(?<class>(?<=]).*.+(?=->)).*.+(?<=->)(?<logmessage>.*)" }
            add_field => { "resultmessage" => "%{logmessage}" }
            break_on_match => false
        }
        grok {
            match => {
                "message" => ".*.+(?<=Consume Time：)(?<millisecond>.+(?= ms)) ms (?<dateTime>\d+\-\d+\-\d+ \d+\:\d+\:\d+).*Execute SQL：(?<SQL>.*)"
            }
        }
        mutate {
            gsub => ["resultmessage", "\n", "::",
                "resultmessage","\tat",""]
        }
        mutate {
            split => {"resultmessage" => "::" }
        }
        mutate {
            convert => { "millisecond" => "integer" }
        }
    }
}

output {
    stdout { codec => rubydebug }
    if[type] == "erp" {
        elasticsearch {
            hosts => ["http://localhost:9200"]
            index => "erp-log"
        }
    }
    if[type] == "mysql" {
        elasticsearch {
            hosts => ["http://localhost:9200"]
            index => "%{[@metadata][beat]}-%{[@metadata][version]}-erp-mysql"
        }
    }
}
```