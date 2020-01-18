---
layout: mypost
title: 测试Gerrit SSH连接
categories: [Gerrit]
---

## 测试Gerrit SSH连接

Gerrit安装好之后，我们需要只有将gerrit上的clone下来，才能进行修改提交，并进行review。

gerrit支持两种方式下载代码：ssh和http。

使用ssh的连接操作如下：，gerrit网址url为：http://review.gerritsite.com/

### 1.本地生成ssh key，并贴到gerrit上。

    
   + 使用ssh-keygen命令，可以在本地账户的.ssh目录下生成公钥和私钥
   + 登录gerrit，将~/.ssh/id_rsa.pub，复制粘贴到gerrit复制到：http://review.gerritsite.com/#/settings/ssh-keys，可以添加多个。
   
### 2.测试链接

    公钥添加到gerrit上之后，就可以通过ssh命令来测试连接，gerrit的ssh默认端口号是29418;如果出现如下内容，表示连接成功。

```
  $ ssh -p 29418 sshusername@review.gerritsite.com

    ****    Welcome to Gerrit Code Review    ****

    Hi John Doe, you have successfully connected over SSH.

    Unfortunately, interactive shells are disabled.
    To clone a hosted Git repository, use:

    git clone ssh://sshusername@review.gerritsite.com:29418/REPOSITORY_NAME.git

  Connection to hostname closed.
  
```

### 3.获取端口信息

    通过curl命令，获取gerrit网站的ssh信息
    
```
$ curl http://review.gerritsite.com/ssh_info
review.gerritsite.com 29418

```