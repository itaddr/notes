<center style="font-weight: bold; font-size: 30px;">Linux基础命令</center>

### 生成ssh密钥

```bash
ssh-keygen -t rsa -b 4096
# 将本机公钥追加到远程主机的公钥新任列表可免密登录，例如本机ssh免密登录本机
~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```



### 时间相关命令

**date** 命令

- 查看当前系统时间：`date`；

- 格式化输出当前系统时间：`date +[format]`，使用`date --help`查看格式化选项说明，例如`date +%F %T.%N`或者`date +%Y-%m-%d %H:%M:%S.%N`格式为`2021-08-09 18:32:33.022139096`；

- 将时间格式字符串输出：`date -d "string"`
  - 时间格式字符串输出：`date -d '2021/08/06'`、`date -d '2021-08-06'`、`date -d '2021/08/06 18:32:44'`、`date -d '2021-08-06 18:32:44`、`date -d '2021-08-06 18:32:44.048338297`
  - 转换格式输出`date -d '2021/08/06' +%Y-%m-%d`
  - 也可以`date --date='2021/08/09'`方式执行
  - 输出明天这个时间的时间：`date -d "tomorrow" +"%F %T"`；
  - 输出昨天这个时候的时间：`date -d "yesterday" +"%F %T"`；
  - 输出前天这个时候的时间：`date -d "2 day ago" +"%F %T"`；
  - 输出后天这个时候的时间：`date -d "-2 day ago" +"%F %T"`；
  - 输出一周前这个时候的时间：`date -d "1 week ago" +"%F %T"`；
  - 输出一个月前这个时候的时间：`date -d "1 month ago" +"%F %T"`；
  - 输出一年前这个时候的时间：`date -d "1 year ago" +"%F %T"`；
  - hour/hours、minute/minutes、second/seconds 等。。。
- 改变数字左填充字符：
  - 不填充：`date +"%Y-%-m-%-d %-H:%-M:%-S"`；
  - 使用空格填充：`date +"%Y-%_m-%_d %_H:%_M:%_S"`；
  - 使用0填充（默认）：`date +"%Y-%0m-%0d %0H:%0M:%0S"`；
  - 输出尽量大写字母：`date +"%Y-%^m-%^d %^H:%^M:%^S"`；
  - 尽可能输出相反的结果：`date +"%Y-%#m-%#d %#H:%#M:%#S"`；
- 将时间戳转换为时间输出：`date --date='@2147483647'`
- 指定时区展示时间：`TZ='Asia/Shanghai' date`
- 查看上海下周五上午9点时间：`date --date='TZ="Asia/Shanghai" 09:00 next Fri'`

- 将文件中的每一行作为`--date`的参数执行一次：`date -f {filename}`
- 展示文件的最后修改时间：`date -r {filename}`或者`date --reference={filename}`
- 查看日历：`cal`
- 设置时间：`date -s 'time_str'`，或者可以按指定格式字符串时间设置`date -s '2021-08-06 18:21:33' +"%F %T" `，特别注意执行完之后需要将系统时间写入bios `hwclock -w`；
- 设置时区：linux通用命令`tzselect`，readhat或centos `timeconfig`，Debian `dpkg-reconfigure tzdata`。或者直接软连接文件`ln -s /usr/share/zoneinfo/$主时区/$次时区 /etc/localtime`如设置亚洲上海`ln -s /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime`



**uptime** 命令

执行结果：

```
[root@abc /] uptime
 15:19:26 up 430 days, 22:08,  6 users,  load average: 3.69, 4.61, 5.39
```

系统当前时间, 系统的运行时间, 系统的登录用户数, 过去1分钟系统负载的均值, 过去5分钟系统负载的均值, 过去15分钟的系统负载的均值



### 查看登录用户

**w** 命令

执行结果：

```
[root@abc /] w
 15:21:52 up 430 days, 22:11,  6 users,  load average: 4.20, 4.48, 5.23
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0    221.232.173.112  08:44    0.00s  0.17s  0.04s w
root     pts/1    27.16.213.227    14:38    8:16   0.03s  0.03s -bash
root     pts/4    27.16.213.227    Thu10    3:36m 33:26   0.32s -bash
root     pts/6    27.16.213.227    11:13   39:52   0.28s  0.25s clickhouse-client -h 127.0.0.1 --port 9399 -uroot --password -m
root     pts/7    27.16.213.227    14:40   27:12   0.01s  0.01s -bash
root     pts/8    27.16.213.227    14:49   31:12   0.02s  0.02s -bash
```

w命令注意是查看系统当前登录的用户，每个用户当前所做的事情，所有活动的负载对计算机的影响，这个命令结合了uptime 、who 和ps -a 的结果
当然这个命令还有其他选项的。有时间可以研究一下使用的方法

w命令第一列，就跟uptime是一样的。
w命令的第二列，就是系统当前的登录用户，

- User：登录用户名
- TTY：登录后系统分配的终端号
- From：远程主机名，即从哪登录的
- login@：何时登录
- IDLE：用户空闲时间。这是个计时器，一旦用户执行任何操作，改计时器就会被重置。
- JCPU：和终端连接的所有进程占用时间。包括当前正在运行的后台作业占用时间
- PCPU：当前进程所占用时间
- WHAT：当前正在运行进程的命令行



**who** 命令

执行结果：

```
[root@abc /]# who
root     pts/0        2021-08-06 08:44 (221.232.173.112)
root     pts/1        2021-08-06 14:38 (27.16.213.227)
root     pts/4        2021-08-05 10:41 (27.16.213.227)
root     pts/6        2021-08-06 11:13 (27.16.213.227)
root     pts/7        2021-08-06 14:40 (27.16.213.227)
root     pts/8        2021-08-06 14:49 (27.16.213.227)
```



**last** 命令

last是列出最近登录过系统的用户

执行结果：

```
[root@abc /]# last
root     pts/8        27.16.213.227    Fri Aug  6 14:49   still logged in   
root     pts/7        27.16.213.227    Fri Aug  6 14:40   still logged in   
root     pts/1        27.16.213.227    Fri Aug  6 14:38   still logged in   
root     pts/6        27.16.213.227    Fri Aug  6 11:13   still logged in   
root     pts/1        27.16.213.227    Fri Aug  6 09:34 - 13:21  (03:47)    
root     pts/0        221.232.173.112  Fri Aug  6 08:44   still logged in   
root     pts/0        27.16.213.227    Thu Aug  5 18:56 - 19:20  (00:23)    
root     pts/4        27.16.213.227    Thu Aug  5 10:41   still logged in   
root     pts/1        27.16.213.227    Thu Aug  5 09:47 - 21:19  (11:32)    
root     pts/0        27.16.213.227    Thu Aug  5 09:34 - 18:22  (08:47)    
root     pts/0        27.16.213.227    Wed Aug  4 20:54 - 23:19  (02:24)    
root     pts/0        27.16.213.227    Wed Aug  4 19:20 - 20:51  (01:30)    
root     pts/1        27.16.213.227    Wed Aug  4 17:40 - 17:42  (00:02)    
root     pts/0        27.16.213.227    Wed Aug  4 08:33 - 17:45  (09:12)
...
```







