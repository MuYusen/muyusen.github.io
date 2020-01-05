---
layout: mypost
title: windows10 安装配置docker环境
categories: [Docker]
---

# windows10 安装配置docker环境

参考：

在Windows平台上搭建Docker开发环境：[http://blog.csdn.net/u011054333/article/details/70064285](http://blog.csdn.net/u011054333/article/details/70064285)

在Windows 10 上安装Hyper-V：[https://docs.microsoft.com/zh-cn/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v](https://docs.microsoft.com/zh-cn/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)

## 环境准备

1. `升级windows 10`：

升级是为了避免后面操作的问题，在启动Hyper-V的时候，怎么都启动不了，报错0x80070057，后来把windows 10 更新了就可以了。

进入微软官网，搜“MediaCreation tool”，在结果页面里选择第一项“下载windows 10”，在新页面里选择第一项“立即更新”，然后弹出下载对话框，下载完成后点击运行，进行升级。升级之后就可以开启了。

2. `启动BIOS的虚拟化设置 | 开启CPU虚拟化支持。`

重启电脑后按F2或F10进入BIOS界面（不同主板型号进入BIOS所需按键不同）。

![](c4757b54d436e88d2f96ec543730d2bc.jpg)

3. `启动Hyper-V`

    + 右键单击 Windows 按钮并选择“程序和功能”。
    + 选择“打开或关闭 Windows 功能”。
    + 选择“Hyper-V”，然后单击“确定”。

![](enable_role_upd.png)

安装完成后，系统会提示你重新启动计算机。

## 安装docker

首先打开[Docker下载页面](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)，然后下载Docker For Windows安装包。注意这个安装包是针对最新的Windows 10系统的，如果使用Windows 8之类的，请下载旧版本的Docker。

安装之后可能会弹出这么一个对话框，提示我们说HyperV未开启，询问我们是否要开启HyperV，我们选择是，然后电脑会重启。之后一切就准备就绪了。 

![](832668-021f8dd5487512f6.png)

电脑重启之后，我们应该可以在任务栏上找到一个Docker图标，右键点击选择version会弹出这样的对话框。如此一来，Docker就安装完毕了。

![](832668-825ca6e2070c2cbe.png)

## 结束

Win+X，选择”Windows PowerShell“，就可以使用常用的docker 命令操作。