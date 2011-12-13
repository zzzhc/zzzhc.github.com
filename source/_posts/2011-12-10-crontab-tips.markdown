---
layout: post
title: "crontab tips"
date: 2011-12-10 12:02
comments: true
categories: shell
---

## crontab command

``` bash
# 设置编辑器
$ export EDITOR=vim
# 编辑
$ crontab -e [-u user]
# 列出crontab内容
$ crontab -l [-u user]
# 删除crontab文件
$ crontab -r [-u user]
```

## crontab syntax

```
* * * * *  command
| | | | |
| | | | |- day of week(0-6) 0 means sunday
| | | |--- month(1-12)
| | |----- day of month(1-31)
| |------- hour
|--------- minute(0-59)

* 表示的项可以用,分隔指定多个值(如5,15,25)，也可以指定周期，如在minute项上写*/5表示每5分钟执行一次
```

## crontab environment

cron脚本执行的环境跟正常用户执行的有区别，~/.bashrc不会被执行, 这一点经常会引起问题, 可以在crontab里设置环境变量来减少影响.

``` bash
# PATH, SHELL, MAILTO比较常用, 如
PATH=/usr/bin:/usr/sbin:/bin:/sbin
SHELL=/bin/bash
MAILTO=xxx@xxx.com

1 * * * * find /var/data/upload/ -mtime 30 -exec rm -- {} +
```

默认情况crontab按OS时区调度, 而不是用户时区，可以通过指定TZ环境变量设实际要用的时区

``` bash
TZ=UTC
1 * * * * find /var/data/upload/ -mtime 30 -exec rm -- {} +
```

# crontab notification

如果command有输出(stdout/stderr)，crond会发送通知邮件, 具体发送给谁可以通过MAILTO定义，默认发送给当前用户。
一般情况stdout可以忽略，所以经常会看到crontab里有 command >/dev/null; stderr一定不要忽略，否则cron job有错误无法正常执行都不知道。

