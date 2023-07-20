---
layout: mypost
title: Automotive Grade Linux (AGL) documentation
categories: [Automotive,AGL]
---

![AGL1](agl.webp)

# Welcome to the Automotive Grade Linux (AGL) documentation

## AGL 是啥？

AGL是一个开源协作项目，它将汽车制造商、供应商和技术公司聚集在一起，旨在为汽车应用程序构建基于Linux的开源软件平台，这些平台可以作为事实上的行业标准。

AGL包含车辆中的所有软件：信息娱乐、仪表盘、抬头显示器（HUB）、远程信息处理、联网汽车、高级驾驶员辅助系统（ADAS）、功能安全和自动驾驶。

AGL是一个Linux基金会项目，其目标如下：

+ 为整个行业构建一个单一平台
+ 开发70～80%的生产项目
+ 通过结合最好的开源项目减少碎片化
+ 统一一个平台开发一个由开发人员、供应商和专业知识组成的生态系统

## 使用不同版本的AGL预编译镜像

[参考](https://docs.automotivelinux.org/en/octopus/#01_Getting_Started/01_Quickstart/01_Using_Ready_Made_Images/)

## 从源码开始编译构建

![image-developer-workflow](image-developer-workflow.png)

### 环境准备

    **支持Yocto编译环境，BitBake**

+ Linux 机器：[参考](https://docs.yoctoproject.org/ref-manual/system-requirements.html#supported-linux-distributions)
+ 至少100G硬盘空间
+ 常用工具与版本：Git >= 1.8.3.1、Tar >= 1.27、Python >= 3.4.0

### 代码下载

[参考](https://wiki.automotivelinux.org/agl-distro/source-code)

#### 定义顶级目录

    export AGL_TOP=$HOME/AGL
    echo 'export AGL_TOP=$HOME/AGL' >> $HOME/.bashrc
    mkdir -p $AGL_TOP

#### 配置repo

    mkdir -p $HOME/bin
    export PATH=$HOME/bin:$PATH
    echo 'export PATH=$HOME/bin:$PATH' >> $HOME/.bashrc
    curl https://storage.googleapis.com/git-repo-downloads/repo > $HOME/bin/repo
    chmod a+x $HOME/bin/repo

#### 源码下载

    cd $AGL_TOP
    mkdir <<branch name>>
    cd <<branch name>>
    repo init -b <<branch name>> -u https://gerrit.automotivelinux.org/gerrit/AGL/AGL-repo
    repo sync

#### 代码目录结构

    $ tree -L 1
    .
    ├── bsp
    ├── external
    ├── meta-agl
    ├── meta-agl-cluster-demo
    ├── meta-agl-demo
    ├── meta-agl-devel
    ├── meta-agl-extra
    └── meta-agl-telematics-demo
