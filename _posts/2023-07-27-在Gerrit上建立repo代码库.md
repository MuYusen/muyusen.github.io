---
layout: mypost
title: 在Gerrit上建立repo代码库
categories: [Gerrit,repo]
---

# 在Gerrit上建立repo代码库

像AOSP和Automotive的仓库一般是使用repo管理的多库形式，所以当项目开始时，我们需要将我们的代码推到Gerrit服务器上。

一般有两种方法：一种是直接将代码镜像下载或者复制到Gerrit服务器的git目录下面，另一种是下载一套源码，然后git push到Gerrit上的指定仓库的特定分支。

为了能统一控制权限，建议在Gerrit服务器上先建立个父项目（EX：code），并在git目录下建一个code目录。

以下详述两种方法和基本命令，以[AGL代码](https://docs.automotivelinux.org/en/octopus/#01_Getting_Started/02_Building_AGL_Image/03_Downloading_AGL_Software/)为例。

## repo mirror 方式

### 下载mirror 格式代码

repo init 命令后面加上 **--mirror** 参数

    repo init -b <<branch name>> -u https://gerrit.automotivelinux.org/gerrit/AGL/AGL-repo --mirror
    repo sync

### gerrit flush-caches

mirror代码下载之后，在Gerrit web上还看不到这些仓库，需要清一下缓存让Gerrit服务加载到这些仓库。

    ssh -p 29418 review.example.com gerrit flush-caches --all

### 修改权限

使用repo forall 命令批量设置仓库父项目，方便权限控制。

    repo forall -c 'echo $REPO_PROJECT;ssh -p 29418 review.example.com gerrit set-project-parent --parent code code/$REPO_PROJECT;'

### 优缺点

+ 命令操作简单
+ Gerrit上会存在代码仓库的所有tag和分支信息
+ 如果是多套代码，会造成代码仓库的冗余

## 源代码 push 方式

### 下载源代码

        repo init -b <<branch name>> -u https://gerrit.automotivelinux.org/gerrit/AGL/AGL-repo
        repo sync

### 使用repo forall 命令在Gerrit上新建仓库

    repo forall -c 'echo $REPO_PROJECT;ssh -p 29418 review.example.com gerrit create-project code/$REPO_PROJECT --parent code;'

### git push 代码到分支

    repo forall -c 'echo $REPO_PROJECT;git push review.example.com:29418/code/$REPO_PROJECT -o skip-validation HEAD:refs/heads/branchname;'

+ 因为有些仓库manifest文件中使用 **clone-depth=1** 的方式，对于这些仓库，需要先把这些参数去掉，下载完整代码。
+ Gerrit允许绕过代码审查直接推送到分支的最大提交数（receive.maxBatchCommits）是 **10000** ，使用 **-o skip-validation** 参数可以跳过验证，同时push 用户需要有如下权限：Forge Author、Forge Committer、Forge Server、Push Merge Commits。

### 优缺点

+ 推荐
+ 父项目只控制权限，不分目录重复建库，避免冗余

不论使用哪种方式，最后都需要修改manifest文件或gitconfig文件，方便从我们的Gerrit服务器上下载代码。
