---
layout: article
title: celery your task --自动化平台初体验
---

自动化平台的搭建比较重要的点是任务的调用分配以及任务的管理，这里引入celery作为整个平台的任务调度器，同时提供api管理任务注册，管理功能，整个框架包括web前端，web后端，任务调度服务，任务执行worker,数据存储。这里我们只谈谈任务调度celery作为服务层的应用。

## 技术栈

 -  vue 
 -  flask + flask_restful
 -  celery + rabbitmq-server
 -  express + mocha
 -  mongodb

## 架构图

<img src="/assets/celery.png">


## 环境配置

在机器A上配置rabbit-server 及任务

### 1. 配置rabbitmq-server

```
$ yum install -y rabbitmq-server
$ service rabbitmq-server start
$ rabbitmqctl add_user user password
$ rabbitmqctl add_vhost vhost
$ rabbitmqctl set_permissions -p vhost user ".*" ".*" ".*"
$ service rabbitmq-server start

```
### 2. 配置celery

```
$ mkdir celery & cd celery
$ virtualenv venv
$ pip install celery

```
### 3. 创建任务

```python
	# tasks.py
	import time
	from celery import Celery

	celery = Celery('tasks', broker='amqp://user:pasword@ip:port/vhost')

	@celery.task
	def add(x,y):
	    return x + y
```

## 环境配置

在机器B上配置celery worker及任务

### 1. 启动任务

```
$ . venv/bin/activate
$ celery -A tasks worker -l info

```

## 测试任务执行

在机器A上produce task

### 1. 生产任务

```
$ . venv/bin/activate
$ python

```
执行代码

```python
	from tasks import add
	add.delay(1,2)

```

机器B会cusumer任务并执行返回3

## 任务管理

受自动化测试的周期，机器数量等因素，确定自动化任务与任务调度解耦，增加一层API管理，提供注册，删除，等功能，每个任务都提供多线程执行，执行结构持久化,

### 1. 任务实例

celery中任务调度

```python
	
	@app.task
	def demo():
	    url = "http://localhost:3000"
	    res = requests.get(url)
	    return res.content


```

具体任务执行

```javascript
	
	app.get('/', function(req, res) {
	    var child = child_process.fork('./runmocha.js',['123'], {
	        silent: true
	    });
	    child.stdout.setEncoding('utf8');
	    child.stdout.on('data', function(data){
	        console.log(data);
	    });
	    res.send("running");
	})


```

## 其他

任务执行的参数由前端WEB传入，执行的状态，结果存入数据库，如果需要实时日志输出，可引入socketio.
