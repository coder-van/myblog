---
title: docker
tags:
categories: out-code
---

docker是个牛X的东西，终于，终于，在今天实践了一把使用docker的舒爽

Ubuntu安装docker [文档](https://store.docker.com/editions/community/docker-ce-server-ubuntu)

docker run -d -p 49001:8080 -v $PWD/jenkins:/var/jenkins_home -t jenkins

docker 安装Gitlab [文档](https://docs.gitlab.com/omnibus/docker/)

```
sudo docker run --detach \
    --hostname gitlab.example.com \
    --publish 443:443 --publish 80:80 --publish 22:22 \                                                                                                                                                                                                                                                                                                                               
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```

docker swarm join \
   --token SWMTKN-1-5pz1yxb9swwacpl30rtfy6quyiq26oe2o8j0bqixgxykkwwm0c-54z53ax5v3ech22evf23c3way \
   182.92.71.176:2377

 docker run --rm -ti -v /var/run/docker.sock:/var/run/docker.sock -e DOCKER_HOST dockercloud/client inspero/ai-prod


 Endpoint:  http://192.168.0.3:9000  http://127.0.0.1:9000
 AccessKey: UJOW9IKVXIRBLF7ZFCF3
 SecretKey: fYSDsBF74nzX0SOcyFVUHIZxNVHnLlIp7mZaOFTj
