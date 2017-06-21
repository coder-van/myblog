---
title: 不同服务使用不同的ssh公钥
date: 2017-05-27
tags: ssh
categories: out-code
---

来到公司后直接用了别人用过的电脑，没有格式化。直接可以ssh到公司的各个服务器，不用重新配置，因为配置公钥到登录服务器必须找领导去设置，所以我就直接用了。不过问题来了，这个公钥被绑定了github账号，怎么破？

解决方法如下：

```
cp ~/.ssh/id_rsa ~/.ssh/id_rsa_copy
cp ~/.ssh/id_rsa.pub ~/.ssh/id_rsa_copy.pub
```

然后设置 ~/.ssh/config

```
Host server1
    HostName 123.45.67.89
    Port 22
    User me
    IdentityFile ~/.ssh/id_rsa_copy
```
然后按照 [文档](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) 生成 ~/.ssh/id_rsa 进行设置就行。

 给github生成一个指定的公钥密钥，~/.ssh/id_rsa_git,然后设置 ~/.ssh/config
 ```
 Host github.com
    port 22
    IdentityFile ~/.ssh/id_rsa_git
 ```

 如果你有两个github账号怎么办，一个公司，一个个人的

 这样你就需要给github设置一个别名，比如 github2.com, 然后一个账号的url就要用 github2.com 代替 github.com

 设置 ~/.ssh/config

 ```
 Host github.com
    port 22
    user git
    IdentityFile ~/.ssh/id_rsa_git

 Host github2.com
     HostName github.com
     Port 22
     User git
     IdentityFile ~/.ssh/id_rsa_git2
 ```
 记得修改项目 .git/config文件的url github2.com 代替 github.com, 并且一定要在用git协议

 记得在github setting 里设置自己新加的ssh-key

 ssh-add -K 
