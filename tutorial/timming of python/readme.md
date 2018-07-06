[toc]
总结了下python常见的两种定时器，一种是比较轻量级的schedule，另外一种是比较健全的celery

环境：python2.7
# 1.schedule
schedule是一种轻量级的模块，在实现比较小的定时任务的时候可以放弃celery转而使用schedule
下面介绍一下使用方法
新建一个schedule.py文件
```
# -*- coding: utf-8 -*-
# @Author: MengYu'
import time
import schedule
import os


def lpush():
    os.system("python lpush.py")


def data_handle():
    os.system("python data_handler.py")


schedule.every().day.at("09:00").do(lpush)
schedule.every().monday.at("10:00").do(data_handle)

while True:
    schedule.run_pending()
    time.sleep(1)

```
# 2.celery
Celery 是一个强大的分布式任务队列，它可以让任务的执行完全脱离主程序，甚至可以被分配到其他主机上运行。我们通常使用它来实现异步任务（ async task ）和定时任务（ crontab ）。
Celery 主要包含以下几个模块：
任务模块
包含异步任务和定时任务。其中，异步任务通常在业务逻辑中被触发并发往任务队列，而定时任务由 Celery Beat 进程周期性地将任务发往任务队列。
消息中间件 Broker
Broker ，即为任务调度队列，接收任务生产者发来的消息（即任务），将任务存入队列。 Celery 本身不提供队列服务，官方推荐使用 RabbitMQ 和 Redis 等。
任务执行单元 Worker
Worker 是执行任务的处理单元，它实时监控消息队列，获取队列中调度的任务，并执行它。
任务结果存储 Backend
Backend 用于存储任务的执行结果，以供查询。同消息中间件一样，存储也可使用 RabbitMQ, Redis 和 MongoDB 等。
## 2.1定时任务
Celery 除了可以执行异步任务，也支持执行周期性任务（ Periodic Tasks ），或者说定时任务。 Celery Beat 进程通过读取配置文件的内容，周期性地将定时任务发往任务队列
文件目录如下：
celery_demo                    # 项目根目录

    ├── celery_task             # 存放 celery 相关文件
        ├── __init__.py
        ├── celeryconfig.py    # 配置文件
        ├── obtain_data_task.py     # 任务文件
        
__init__.py文件如下：
```
# coding:utf-8
# @Author: MengYu
from __future__ import absolute_import
from celery import Celery
# 实例化Celery,必须叫app, 参数名随意
app = Celery('celery_task')
# 指定配置文件的位置
app.config_from_object('celery_task.celeryconfig')
```
celeryconfig.py文件如下：
```
# coding:utf-8
# @Author: MengYu
from datetime import timedelta

from celery.schedules import crontab

# 在这里我们使用Redis作为调度队列，当然也可以使用官方推荐的rabitmq
BROKER_URL = 'redis://root:123456@172.18.100.103:6379/9'
CELERY_RESULT_BACKEND = 'redis://root:123456@172.18.100.103:6379/9'
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'
CELERY_ACCEPT_CONTENT=['json']
# 指定时区为中国时区
CELERY_TIMEZONE = 'Asia/Shanghai'
# 指定要执行的任务obtain_data_task的位置
CELERY_IMPORTS = ('celery_task.obtain_data_task',)
# 定时调度
CELERYBEAT_SCHEDULE = {
    'crawl_all': {
        'task': 'celery_task.obtain_data_task.crawl',
        # 每天早上九点执行一次任务
        # 'schedule': crontab(hour=9, minute=0),
        # 每隔三十秒执行一次
        'schedule': timedelta(seconds=30),
    },
}
```
obtain_data_task.py文件如下：
```
# coding:utf-8
# @Author: MengYu
import os
# 从celery_task(你建的目录)导入app
from celery_task import app


@app.task
def crawl():
    """celery -B -A celery_task --loglevel=info"""
    print '*'*20
    os.system("python run.py")

if __name__ == '__main__':
    crawl()
```
现在，让我们启动 Celery Worker 进程，在项目的根目录下执行下面命令：
```
celery -A celery_task worker --loglevel=info
 ```
接着，启动 Celery Beat 进程，定时将任务发送到 Broker ，在项目根目录下执行下面命令：
```
celery beat -A celery_task
```
在上面，我们用两个命令启动了 Worker 进程和 Beat 进程，我们也可以将它们放在一个命令中：
```
celery -B -A celery_task worker --loglevel=info
```
