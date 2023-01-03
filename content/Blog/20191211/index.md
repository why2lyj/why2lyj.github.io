---
title: "Python 修改 logging 时间"
date: 2019-12-11 22:53:00
draft: false
hideLastModified: true
tags: ["Python"]
summary: "Python Logging模块修改默认时间"
summaryImage: "summary.jpg"
weight: 423971882
---

## 发生环境：

*Python3+、docker-18.03*

## 问题描述：

　　最近需要写日志来查看下程序的总体运行情况，另外代码是直接丢进`docker`中部署的，发现输出的日志时间与本地时间相差8小时，因为`docker`的本地时间在镜像`build`时候就设置成了北京时间，所以第一个感觉是`logging`模块的默认时间可能是根本问题。

日志输出的时间：
```python
2019-12-11 14:01:16.00276 INFO:apscheduler.executors.default:Running job
```

查询本地时间（docker容器内部时间）：
```shell
Wed Dec 11 22:01:16 CST 2019
```

*搜了很多地方，最后还是很无奈的妥妥地去了官网
`https://docs.python.org/3/howto/logging.html`*

有一段话解释了logging使用的时间：

> Formatters use a user-configurable function to convert the creation time of a record to a tuple. By default, time.localtime() is used; to change this for a particular formatter instance, set the converter attribute of the instance to a function with the same signature as time.localtime() or time.gmtime(). To change it for all formatters, for example if you want all logging times to be shown in GMT, set the converter attribute in the Formatter class (to time.gmtime for GMT display).

**总结一下就是**：`logging`默认使用的是`time.localtime()`，如果要使用其他时间需要使用`Formatter`下的`converter`重定义。`time.localtime()`的时间使用的是
[time.time()](https://docs.python.org/3/library/time.html#time.localtime), 而`time.time()`返回的是[UTC时间](https://docs.python.org/3/library/time.html#time.time)，即从 1970-1-1 00:00:00 到现在的秒数，因此时间相差8小时。


## 解决办法：
最后改了下代码：

```python
import datetime
import logging

def CST():
    # UTC与CST相差8小时
    cst = datetime.datetime.now() + datetime.timedelta(hours=8)
    return cst.timetuple()

# 修改日志默认时区
logging.Formatter.converter = CST

logging.basicConfig(level=logging.INFO,
                    format='%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s',
                    datefmt='%Y-%m-%d %H:%M:%S',
                    filename='log.txt',
                    filemode='a')

```