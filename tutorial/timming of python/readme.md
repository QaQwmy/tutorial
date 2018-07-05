�ܽ�����python���������ֶ�ʱ����һ���ǱȽ���������schedule������һ���ǱȽϽ�ȫ��celery
������python2.7


1.schedule
2.celery
2.1��ʱ����
1.schedule
schedule��һ����������ģ�飬��ʵ�ֱȽ�С�Ķ�ʱ�����ʱ����Է���celeryת��ʹ��schedule
�������һ��ʹ�÷���
# -*- coding: utf-8 -*-
# @Author: MengYu
import time
import schedule
import os


def lpush():
    os.system("python lpush.py")


def data_handle():
    os.system("python data_handler.py")


schedule.every().day.at("09:00").do(lpush)   # ÿ�����Ͼŵ�ִ��һ��
schedule.every().monday.at("10:00").do(data_handle) # ÿ��һ����ʮ��ִ��һ��

# Ϊ�˱���scheduleģ��ִ�У�����ʹ����ѭ��
while True:
    schedule.run_pending()
    time.sleep(1)
2.celery
Celery ��һ��ǿ��ķֲ�ʽ������У��������������ִ����ȫ�����������������Ա����䵽�������������С�����ͨ��ʹ������ʵ���첽���� async task ���Ͷ�ʱ���� crontab ����
Celery ��Ҫ�������¼���ģ�飺
����ģ��
�����첽����Ͷ�ʱ�������У��첽����ͨ����ҵ���߼��б�����������������У�����ʱ������ Celery Beat ���������Եؽ�������������С�
��Ϣ�м�� Broker
Broker ����Ϊ������ȶ��У��������������߷�������Ϣ�������񣩣������������С� Celery �����ṩ���з��񣬹ٷ��Ƽ�ʹ�� RabbitMQ �� Redis �ȡ�
����ִ�е�Ԫ Worker
Worker ��ִ������Ĵ���Ԫ����ʵʱ�����Ϣ���У���ȡ�����е��ȵ����񣬲�ִ������
�������洢 Backend
Backend ���ڴ洢�����ִ�н�����Թ���ѯ��ͬ��Ϣ�м��һ�����洢Ҳ��ʹ�� RabbitMQ, Redis �� MongoDB �ȡ�
2.1��ʱ����
Celery ���˿���ִ���첽����Ҳ֧��ִ������������ Periodic Tasks ��������˵��ʱ���� Celery Beat ����ͨ����ȡ�����ļ������ݣ������Եؽ���ʱ�������������
�ļ�Ŀ¼����celery_demo                    # ��Ŀ��Ŀ¼
    ������ celery_task             # ��� celery ����ļ�
        ������ __init__.py
        ������ celeryconfig.py    # �����ļ�
        ������ task1.py           # �����ļ�

__init__.py�ļ����£�
# coding:utf-8
# @Author: MengYu
from __future__ import absolute_import
from celery import Celery
# ʵ����Celery,�����app, ����������
app = Celery('celery_task')
# ָ�������ļ���λ��
app.config_from_object('celery_task.celeryconfig')

celeryconfig.py�ļ����£�
# coding:utf-8
# @Author: MengYu
from datetime import timedelta

from celery.schedules import crontab

# ����������ʹ��Redis��Ϊ���ȶ��У���ȻҲ����ʹ�ùٷ��Ƽ���rabitmq
BROKER_URL = 'redis://root:123456@172.18.100.103:6379/9'
CELERY_RESULT_BACKEND = 'redis://root:123456@172.18.100.103:6379/9'
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'
CELERY_ACCEPT_CONTENT=['json']
# ָ��ʱ��Ϊ�й�ʱ��
CELERY_TIMEZONE = 'Asia/Shanghai'
# ָ��Ҫִ�е�����obtain_data_task��λ��
CELERY_IMPORTS = ('celery_task.obtain_data_task',)
# ��ʱ����
CELERYBEAT_SCHEDULE = {
    'crawl_all': {
        'task': 'celery_task.obtain_data_task.crawl',
        # ÿ�����Ͼŵ�ִ��һ������
        # 'schedule': crontab(hour=9, minute=0),
        # ÿ����ʮ��ִ��һ��
        'schedule': timedelta(seconds=30),
    },
}
task1.py�������£�
# coding:utf-8
# @Author: MengYu
import os
# ��celery_task(�㽨��Ŀ¼)����app
from celery_task import app


@app.task
def crawl():
    """celery -B -A celery_task --loglevel=info"""
    print '*'*20
    # Ҫִ�е�shell���� 
    os.system("python run.py")

if __name__ == '__main__':
    crawl()

���ڣ����������� Celery Worker ���̣�����Ŀ�ĸ�Ŀ¼��ִ���������
 celery -A celery_task worker --loglevel=info
���ţ����� Celery Beat ���̣���ʱ�������͵� Broker ������Ŀ��Ŀ¼��ִ���������
celery beat -A celery_task
�����棬�������������������� Worker ���̺� Beat ���̣�����Ҳ���Խ����Ƿ���һ�������У�
$ celery -B -A celery_task worker --loglevel=info



