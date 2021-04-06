### 通常杀死一个进程的方法

```shell
kill -9 ${pid}
```

这样会出现几个问题

- 你必须得自动这个程序得pid是多少才能够杀死

```shell
ps -ef | grep 'init' | grep -v grep | awk '{print $2}' | xargs kill -s SIGINT
```

来解析一条这个 shell 命令

```shell
ps -ef 
```

- -e 是表示显示所有得进程
  ```shell
    k@DESKTOP-1RLL7FE:~$ ps -e
    PID TTY          TIME CMD
    1 ?        00:00:00 init
    8 tty1     00:00:00 init
    9 tty1     00:00:00 bash
    101 tty1     00:00:00 ps
    ```
- -f 是格式化输出进程信息
  ```shell
    k@DESKTOP-1RLL7FE:~$ ps -ef
    UID        PID  PPID  C STIME TTY          TIME CMD
    root         1     0  0 09:30 ?        00:00:00 /init
    root         8     1  0 09:30 tty1     00:00:00 /init
    k            9     8  0 09:30 tty1     00:00:00 -bash
    k          102     9  0 09:37 tty1     00:00:00 ps -ef
    ```

```shell
ps -ef | grep 'init' 
```

- grep 'init'

  显示关于init的进程

    ```shell
    k@DESKTOP-1RLL7FE:~$ ps -ef | grep 'init'
    root         1     0  0 09:30 ?        00:00:00 /init
    root         8     1  0 09:30 tty1     00:00:00 /init
    k          104     9  0 09:48 tty1     00:00:00 grep --color=auto init
    ```

```shell
ps -ef | grep 'init' | grep -v grep
```

- grep -v grep

  不匹配这个grep的进程,因为grep 'init' 也算一个进程

    ```shell
    k@DESKTOP-1RLL7FE:~$ ps -ef | grep 'init' | grep -v grep
    root         1     0  0 09:30 ?        00:00:00 /init
    root         8     1  0 09:30 tty1     00:00:00 /init
    ```

```shell
ps -ef | grep 'init' | grep -v grep | awk '{print $2}'
```

- awk '{print $2}'

  显示第二列的所有信息 awk 文本处理语言

  ```shell
  k@DESKTOP-1RLL7FE:~$ ps -ef | grep '/init'| grep -v grep | awk '{print $2}'
  1
  8
    ```

```shell
ps -ef | grep 'init' | grep -v grep | awk '{print $2}' | xargs kill -s SIGINT
```

- xargs kill -s SIGINT

  将前面输入的作为执行的命令参数 也就是相当于会执行

  ```shell
    kill -s SIGINT 1
    kill -s SIGINT 8
  ```