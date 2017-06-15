---
layout: post
title: email your echarts
---

数据汇总后，如果有报表类邮件输出需求，可以简单的用echarts后台输出分析数据，然后通过邮件脚本
定时发送

## 环境

* ubuntu 16.04
* nodejs
* [echarts](https://github.com/ecomfe/echarts)
* 自适应邮件模板[salted](https://github.com/rodriguezcommaj/salted)
* 图片生成器[node-echarts](https://github.com/suxiaoxin/node-echarts)
* 邮件[emailjs](https://github.com/eleith/emailjs)

### node-echarts 生成图片
##### 获取你的数据，然后生成echarts 的option
<pre><code>
    var option = {
        backgroundColor: '#2c343c',

        title: {
            text: 'Customized Pie',
            left: 'center',
            top: 20,
            textStyle: {
                color: '#ccc'
            }
        },

        tooltip : {
            trigger: 'item',
            formatter: "{a} <br/>{b} : {c} ({d}%)"
        },

        visualMap: {
            show: false,
            min: 80,
            max: 600,
            inRange: {
                colorLightness: [0, 1]
            }
        },
        series : [
            {
                name:'访问来源',
                type:'pie',
                radius : '55%',
                center: ['50%', '50%'],
                data:[
                    {value:335, name:'直接访问'},
                    {value:310, name:'邮件营销'},
                    {value:274, name:'联盟广告'},
                    {value:235, name:'视频广告'},
                    {value:400, name:'搜索引擎'}
                ].sort(function (a, b) { return a.value - b.value}),
                roseType: 'angle',
                label: {
                    normal: {
                        textStyle: {
                            color: 'rgba(255, 255, 255, 0.3)'
                        }
                    }
                },
                labelLine: {
                    normal: {
                        lineStyle: {
                            color: 'rgba(255, 255, 255, 0.3)'
                        },
                        smooth: 0.2,
                        length: 10,
                        length2: 20
                    }
                },
                itemStyle: {
                    normal: {
                        color: '#c23531',
                        shadowBlur: 200,
                        shadowColor: 'rgba(0, 0, 0, 0.5)'
                    }
                }
            }
        ]
    };)
        time.sleep(1)
</code></pre>
##### 设置图片格式，保存路径后生成
<pre><code>
node_echarts({
    path: __dirname + '/bar.png',
    option: option,
    width: 1000,
    height: 500
})
</code></pre>
### 邮件发送
##### 嵌入图片，生成邮件模板,my-image是emailjs附件中图片的名字
<pre><code>
   img src="cid:my-image" width="240" height="130" 
</code></pre>
##### 配置emailjs 发送html邮件
> 配置邮箱协议
<pre><code>
    var server = email.server.connect({
        user: "email",
        password: "********",
        host: "smtp.*.com",
        port: your port,
        ssl: true
    });
</code></pre>
> 配置邮件内容
<pre><code>
     var message    = {
       text:    "i hope this works", 
       from:    "you <username@your-email.com>", 
       to:      "someone <someone@your-email.com>, another <another@your-email.com>",
       cc:      "else <else@your-email.com>",
       subject: "testing emailjs",
       attachment: 
       [
          {data:"your html content", alternative:true},
          {path:"path/to/bar.png", type:"image/jpg", headers:{"Content-ID":"<my-image>"}}
       ]
    };
</code></pre>
> 发送
```
server.send(message, function(err, message) { console.log(err || message); });
```


