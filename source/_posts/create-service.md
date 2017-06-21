---
title: 如何打包linux服务安装包
date: 2017-05-13 23:08:40
tags:
- linux
categories: out-code
---

先了解几个打包需要的知识点

打包使用fpm，如果系统没有安装请先安装，fpm 通过 ruby gem 安装，一般ruby 和 gem系统默认已经安装，可直接 执行最后一句

``` code
# 检测 fpm 是否已经安装
  fpm --version
# 1.检测ruby是否已安装，如果没有按照执行第二步，否则第三步
  ruby -v
# 2.安装ruby
  Redhat or Centos: sudo yum install ruby-devel gcc make rpm-build ruby-devel
  Debian or Ubuntu: sudo apr-get install ruby rubygems ruby-devel
# 3.由于墙的原因，修改Ruby仓库到国内的淘宝仓库
  gem sources -a http://ruby.taobao.org/
  gem sources --remove http://rubygems.org/
# 4.安装fpm
  gem install fpm
```
fpm  
---
It should be easy to say “here’s my install dir and here’s some dependencies; please make a package”

\| [官方文档](https://fpm.readthedocs.io/en/latest/)  | [Github](https://github.com/jordansissel/fpm)    |

几个配置服务的linux知识
---
/etc/init.d 包含许多系统服务的启动和停止脚本，常见有：
- ssh sudo 等系统自带服务
- nginx redis 等用户安装服务

/etc/rc.local 在系统启动之后执行的脚本添加到这里

/etc/default 放各种服务缺省参数

systemd  /usr/lib/systemd/system/ 和 /etc/systemd/system/  在进程启动过程中更有效地引导加载服务
- 支持并行化任务
- 同时采用socket式与D-Bus总线式激活服务；
- 按需启动守护进程（daemon）；
- 利用 Linux 的 cgroups 监视进程；
- 支持快照和系统恢复；
- 维护挂载点和自动挂载点；
- 各服务间基于依赖关系进行精密控制。

[Blog](https://blog.linuxeye.cn/400.html)

现在我们通过grafana的服务打包过程来了解具体过程

``` code
# 执行 go run build.go pkg-deb
# 首先会创建一个/tmp/grafana-linux-pack488817500/ 目录来存放服务所需内容
# 然后创建执行文件安装目录 和配置文件目录 目录/tmp/grafana-linux-pack488817500/usr/share/grafana 下的内容最终会被拷贝到/usr/share/grafana，所有目录都是这个规则
# /usr/share/grafana 是web目录
mkdir -p /tmp/grafana-linux-pack488817500/usr/share/grafana
# /etc 下是linux服务所需的shell
mkdir -p /tmp/grafana-linux-pack488817500/etc/grafana
mkdir -p /tmp/grafana-linux-pack488817500/etc/init.d
mkdir -p /tmp/grafana-linux-pack488817500/etc/default
mkdir -p /tmp/grafana-linux-pack488817500/usr/lib/systemd/system
mkdir -p /tmp/grafana-linux-pack488817500/usr/sbin
# 将项目中的执行文件，配置文件及shell脚本等拷贝到/tmp/grafana-linux-pack488817500/
# /usr/sbin/grafana-server 服务可执行文件路径
cp -p /home/ubuntu/go/src/github.com/grafana/grafana/tmp/bin/grafana-server /tmp/grafana-linux-pack488817500/usr/sbin/grafana-server
cp -p /home/ubuntu/go/src/github.com/grafana/grafana/tmp/bin/grafana-cli /tmp/grafana-linux-pack488817500/usr/sbin/grafana-cli
cp -p packaging/deb/init.d/grafana-server /tmp/grafana-linux-pack488817500/etc/init.d/grafana-server
cp -p packaging/deb/default/grafana-server /tmp/grafana-linux-pack488817500/etc/default/grafana-server
cp -p packaging/deb/systemd/grafana-server.service /tmp/grafana-linux-pack488817500/usr/lib/systemd/system/grafana-server.service
cp -a /home/ubuntu/go/src/github.com/grafana/grafana/tmp/. /tmp/grafana-linux-pack488817500/usr/share/grafana
rm -rf /tmp/grafana-linux-pack488817500/usr/share/grafana/bin
# 最后一步就是使用fpm讲准备好的文件打包成deb文件
# Creating package:  deb
fpm -t deb -s dir --description Grafana -C /tmp/grafana-linux-pack488817500 --vendor Grafana --url https://grafana.com --license "Apache 2.0" --maintainer contact@grafana.com --config-files /etc/init.d/grafana-server --config-files /etc/default/grafana-server --config-files /usr/lib/systemd/system/grafana-server.service --after-install packaging/deb/control/postinst --name grafana --version 4.3.0 -p ./dist --deb-no-default-config-files --iteration 1494588959pre1 --depends adduser --depends libfontconfig .
Created package # {:path=>"./dist/grafana_4.3.0-1494588959pre1_amd64.deb"}
```

以上是打包成了deb包（debian和ubuntu系统安装包文件），rpm包和deb稍有不同，但过程基本类似。

最后补充一下，开始的时候在考虑怎么实现daemon，有go-daemon,daemon等框架，基本以复制自己启动一个新进程实现。其实linux的init.d注册的service都是以daemon方式启动，通过fork方式实现。请参考[wiki](https://en.wikipedia.org/wiki/Daemon_(computing))
