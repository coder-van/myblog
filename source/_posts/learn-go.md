---
title: learn-go
date: 2017-03-30 18:02:28
tags: Go语言
categories: code
---

Go 语言学习

在互联网时代，应该有一个能简单驾驭多核和集群，并且开发高效，方便团队配合的语言。

人们需要建立统一的交互语言来降低沟通的成本

  - 代码风格规范
  - 错误处理规范
  - 包管理
  - 契约规范（接口）
  - 单元测试规范
  - 功能开发的流程规范


BLOG

看[GO语言设计的由来][go_design]

包管理
---
因为Go包都是直接使用github上的开源项目，不像Nodejs有npm或者Python的pip那样有有一个正式的方式维护依赖包稳定可靠。[govendor](https://github.com/kardianos/govendor)提供一种解决方法，将当前依赖的项目放置在vendor目录下，随着项目一起提交到仓库，但似乎远没有pip或npm包管理方式那样美丽呢。





[go_design]: http://www.oschina.net/translate/go-at-google-language-design-in-the-service-of-software-engineering
