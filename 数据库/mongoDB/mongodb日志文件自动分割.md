---
title: mongodb日志文件自动分割
date: 2019-04-07
categories: 
- 数据库
- mongoDB
tags: 
- mongoDB
init: 1
---
今天突然接到报警，说服务器磁盘空间不够，很是诧异，最近也没做什么，以为是鹰眼下载的项目数据过大，结果发现并非如此。真正占用空间的大头竟然是mongodb的日志，一个 文件直接占了40G，要知道我mongodb中的数据也就600m。

![](http://img.hksite.cn/2019-03-26-082017.jpg)

所以，今天就着手清理mongodb的日志文件，同时采取一些策略防止此类问题再发生。

要清理日志文件，就必须要先切换日志文件：

## 切换日志文件

根据官方提供的资料，mongodb是采用旋转日志的方式管理日志，即：MongoDB默认情况下不会自动的切换轮转日志的，但是有一个例外：当你使用系统日志（--syslog）的时候，会根据系统的日志切换机制进行日志切换。

>MongoDB only rotates logs in response to the [`logRotate`](https://docs.mongodb.com/manual/reference/command/logRotate/#dbcmd.logRotate) command, or when the [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) or [`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos) process receives a `SIGUSR1` signal from the operating system.

根据文档可以看出，主动让mongodb切换日志有两种途径：

1. 使用logRotate命令
2. 向mongo进程发送`SIGUSR1`命令

### logRotate命令

用mongo连接到服务端

```
> use admin 
> db.auth()  //如果你有账户管理，需要进行登录
> db.runCommand({logRotate:1})
```

这样会使mongo关闭当前日志文件，重启一个新的日志文件，不需要停止mongodb服务。这时候去log目录下可以发现有一个新的以时间戳结尾的文件：

```
mongodb.log  mongodb.log.2019-02-26T08-00-55
```

注： 前提是启动的时候必须是`--logpath`指定了log路径的

### SIGUSR1

查找mongo的服务id

```
ps aux|grep mongo
```
发送-SIGUSR1命令

```
kill -SIGUSR1 <mongod process id>
```



## 日志自动分割

但是上面的方法都还是不够好，因此，我们可以考虑使用脚本进行日志自动分割

```bash
#!/bin/bash
#Rotate the MongoDB logs to prevent a single logfile from consuming too much disk space.

app=mongod

mongodPath=/usr/local/mongodb/bin/

pidArray=$(pidof $mongodPath/$app)

for pid in $pidArray;do
if [ $pid ]
then
    kill -SIGUSR1 $pid
fi
done

exit
```

保存，并命名为logRotate.sh，保存到一个目录/path/to/logRotate.sh，并给一个执行权限

```
chmod a+x /path/to/logRotate.sh
```

编写一个定时任务log.cron

```
* 10 * * * /path/to/logRotate.sh
```

然后执行

```
crontab  ./crontest.cron
```

你可以查看一下你自己的定时任务：

```
$ crontab -l
* 10 * * * /home/hawkeye/mongodb/logRotate.sh
```



附：

定时任务的格式："分 时 日 月 周 命令”。当分为＊时，表示每分钟执行一次，时为＊时，表示每小时执行一次。

```
* 10 * * * doSomething   # 每天10点执行
* */2 * * * doSomething   # 每2小时执行
```