---
title: 使用 gitlab gitlab-ci docker 实现 Flask 项目的CICD
tags: gitlab gitlab-ci gitlab-runner docker
---

CICD(Continuous Integration and Continuous Deployment)即持续集成和持续发布

CI持续集成的功能主要指下图的CI PIPELINE,主要任务包括项目构建，单元测试和集成测试，CD持续发布主要指下图CDPIPELINE部分，主要任务是项目部署。多数情况下人们说CI的时候其实概指CICD。以前都用Jenkins做CI，但如果你的代码是放在自己搭建的Gitlab上，那其实GItlab－CI可能是更好的选择。

![CICD](/images/ci.png)

注：客户端，前端和后端项目都可以有CI的过程，但是只有后端项目需要CD；

首先实现一个简单的Flask项目,这是来自Docker官方文档的一个Flask Demo模块。功能很简单，对访问计数
```
from flask import Flask
from redis import Redis, RedisError
import os
import socket

# Connect to Redis
redis = Redis(host="redis", db=0, socket_connect_timeout=2, socket_timeout=2)

app = Flask(__name__)

@app.route("/")
def hello():
    try:
        visits = redis.incr("counter")
    except RedisError:
        visits = "<i>cannot connect to Redis, counter disabled</i>"

    html = "<h3>Hello {name}!</h3>" \
           "<b>Hostname:</b> {hostname}<br/>" \
           "<b>Visits:</b> {visits}"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

如何部署上线服务：
通过 手动 ｜ 触发 ｜ 定时 ｜ tags

Docs
---
[Gitlab CICD](https://docs.gitlab.com/ee/ci/README.html)
[Gitlab Runner](https://docs.gitlab.com/runner/)
