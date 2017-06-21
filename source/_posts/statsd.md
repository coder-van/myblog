---
title: StatsD
date: 2017-03-28 10:39:01
tags:
- StatsD
- 数据聚合器
- 数据监控
categories: out-code
---

## StatsD
数据聚合器，接受数据，以固定的时间间隔对数据聚合，然后flush到graphite等数据服务。

#### 数据接收器

- UDP server 默认 8125端口
- TCP server 需要在config文件里配置

#### packet格式如下

ex: key1:3|c|@.0.2\nkey2:-10|g 格式的数据packet

key: [value]|[type]|[rate]:[value]|[type]|[rate]:...

key: 对统计的值命名的键值

value: 根据type不同有不同操作

type: 技术器类型 c Counting 计数器 | ms Timer 计时器 | g Gauges 刻度计 | s 检测器； default=c

rate: 计数速率 当type==c or g 时有效 如key:1|c|@0.2 则每次加5

数据之间通过'\n'间隔

#### 几种基本数据计数类型

Counter
---
计数器。flush后重置0，聚合后的数据为计数值和计数速率／秒

计数公式：Metrics.countors[key] ＋= value * (1 / rate) 即default value=1 rate=1.0

数据可以如下传递：
``` code
key: 或者 key:1 或者 key:1|c 或者 key:+1|c|@1.0

key:=-3|c|@0.1
```

Gauge
---
刻度计 记录最后一次的值

当value 有+-符号： Metrics.gauges[key] ＋= value
否则： Metrics.gauges[key] = value
rate值无效

数据可以如下传递：
``` code
key:10|g
key:+4|g|@0.1  @0.1会被忽略 不推荐对guage使用＋—，最好直接赋值
```

Timer
---
计时器。计算平均值，差，和，最大最小，方差，均差，中间值，百分比队列。

了解计时器需要先学习[histogram-直方图，矩型图](http://onlinestatbook.com/2/graphing_distributions/histograms.html)

Metrics.timers[key].push(value);  //defualt: value=0
Metrics.timer_counters[key] += (1/rate);  //defualt: rate=0

数据可以如下传递：
``` code
key:|ms|
key:320|ms|@0.1
```


Sets
----
Sets 记录出现过的value

如果 value != 0
Metrics.sets[key][value]=true  // default value=0

数据格式如下：
``` code
key:765|s
```


Metrics: 统计数据
===
处理前的数据

``` code
{
counters = {};
timers = {};
timer_counters = {};
gauges = {};
sets = {};
}
```

Backend 数据存储处理节点
===
StatsD 从本地发送metrics数据到backend连接的服务, 默认graphite。

StatsD 项目包含了以下backend类型
- [Graphite][graphite] 提供web数据可视化的时间轴数据存储开源项目；
- Console 将数据输出到终端，便于开发调试；
- Repeater 使用 `packet` emit API 推送新数据到多backend的StatsD实例；



TODO
---
实现集群 充分利用多核
用不同语言实现，实现单语言完成数据收集和可视化


[graphite]: http://graphiteapp.org/
[StatD and Graphite]: http://www.tuicool.com/articles/6Jrquye
[blog cloudinsight]: http://cloudinsight.oneapm.com/blog/agentos/
