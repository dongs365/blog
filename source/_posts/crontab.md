---
title: crontab
date: 2017-09-01 11:31:20
tags:
- shell
- 命令
- 网络
categories: Linux
---
crontab命令被用来提交和管理用户的需要周期性执行的任务，与windows下的计划任务类似，当安装完成操作系统后，默认会安装此服务工具，并且会自动启动crond进程，<!-- more -->crond进程每分钟会定期检查是否有要执行的任务，如果有要执行的任务，则自动执行该任务。
# 语法
```
crontab(选项)(参数)
```
# 选项
```
-e：编辑该用户的计时器设置
-l：列出该用户的计时器设置
-r：删除该用户的计时器设置
-u<用户名称>：指定要设定计时器的用户名称
```

# 时间
```
minute hour day month week command   顺序：分 时 日 月 周
```
其中：
minute： 表示分钟，可以是从0到59之间的任何整数。
hour：表示小时，可以是从0到23之间的任何整数。
day：表示日期，可以是从1到31之间的任何整数。
month：表示月份，可以是从1到12之间的任何整数。
week：表示星期几，可以是从0到7之间的任何整数，这里的0或7代表星期日。
command：要执行的命令，可以是系统命令，也可以是自己编写的脚本文件。
在以上各个字段中，还可以使用以下特殊字符：
星号（\*）：代表所有可能的值，例如month字段如果是星号，则表示在满足其它字段的制约条件后每月都执行该命令操作。
逗号（,）：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”
中杠（-）：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”
正斜线（/）：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如*/10，如果用在minute字段，表示每十分钟执行一次

# 其他
用sleep实现秒级运行（每20秒）
```
* * * * * sleep 20; /bin/date
* * * * * sleep 40; /bin/date
```
