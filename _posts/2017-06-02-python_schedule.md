---
layout: post
title: 一个python定时器 - schedule
---

安利一个python定时器-schedule，直观，好用！

## 环境

* ubuntu 16.04
* python2.7
* virtualenv
* schedule
* nohup

### 创建python虚拟环境

```
$ mkdir demo
$ cd demo
$ virtualenv venv
```

### Installing schedule

```
$ pip install schedule
```

### app.py

<pre><code>

    import schedule
    import time

    def job():
        print("I'm working...")
    #每10分钟
    schedule.every(10).minutes.do(job)
    #每小时
    schedule.every().hour.do(job)
    #每2小时
    schedule.every(2).hours.do(job)
    #每天
    schedule.every().day.at("10:30").do(job)
    #每个周一
    schedule.every().monday.do(job)
    #每个周一13点15分
    schedule.every().monday.at("13:15").do(job)

    while True:
        schedule.run_pending()
        time.sleep(1)
</code></pre>

### 执行

```
$ python app.py
```

> 简单部署
```
$ nohup python app.py &
```

