---
layout: mypost
title: Yocto Project Quick Build
categories: [Yocto]
---

# Yocto Project Quick Build

+ 使用Yocto Project构建典型的image镜像
+ 配置特定硬件的build

## Compatible Linux Distribution

确定编译机器配置满足如下要求：

+ 至少90G的空闲硬盘
+ 至少8G内存空间
+ 运行有一个支持的Linux 发行版（Fedora, openSUSE, CentOS, Debian, or Ubuntu）。
+ 软件版本要求:Git >= 1.8.3.1 、tar >= 1.28 、Python >= 3.8.0 、gcc >= 8.0 、GNU make >= 4.0

## Build Host Packages

在编译机器上安装必要的编译软件，Ubuntu系统可使用如下的命令进行安装：

    $sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev python3-subunit mesa-common-dev zstd liblz4-tool file locales
    $sudo locale-gen en_US.UTF-8

## Use Git to Clone Poky

编译机器配置完成以后，使用git 命令从Poky上下载代码，当前以yocto-2.5为例：

    $ git clone git://git.yoctoproject.org/poky
    Cloning into 'poky'...
    remote: Counting
    objects: 432160, done. remote: Compressing objects: 100%
    (102056/102056), done. remote: Total 432160 (delta 323116), reused
    432037 (delta 323000) Receiving objects: 100% (432160/432160), 153.81 MiB | 8.54 MiB/s, done.
    Resolving deltas: 100% (323116/323116), done.
    Checking connectivity... done.

    $ git checkout -t origin/mickledore -b my-mickledore
    Branch 'my-mickledore' set up to track remote branch 'mickledore' from 'origin'.
    Switched to a new branch 'my-mickledore'

## Building Your Image

使用如下命令来构建一个image。这个生成过程是从源代码构建一个包含工具链的完整的Linux发行版。

    Note：
    编译过程中会下载一些东西，如果编译机器没有配置代理，那么在编译过程中会报 Git 或 Fetch的问题。

### 初始化编译环境:Initialize the Build Environment

运行oe-init-build-env脚本在编译机器上构建Yocto项目的编译环境。

    $source oe-init-build-env

执行这个脚本的时候，远在源代码路径（the Source Directory）下创建构建目录（the Build Directory）。脚本执行完，当前的工作目录就被设置成了构建目录。然后，在之后的编译过程中，构建目录保存和编译过程中生成的所有文件。

### 测试本地配置文件：Examine Your Local Configuration File

在设置完编编译环境之后，会在构建目录的conf文件夹下生成一个local.conf文件。默认，是去编译一个qemux86的target，这个target适用于当前的模拟器。这里面所使用的包管理器是RPM包管理器。

    通过使用镜像，可以显著加快编译时间病防止fetch问题。在构建目录的local.conf中加上如下几行，就可以使用镜像。

    BB_SIGNATURE_HANDLER = "OEEquivHash"
    BB_HASHSERVE = "auto"
    BB_HASHSERVE_UPSTREAM = "hashserv.yocto.io:8687"
    SSTATE_MIRRORS ?= "file://.* https://sstate.yoctoproject.org/all/PATH;downloadfilename=PATH"

### 开始编译：Start the Build

继续执行如下的命令，为我们制定的target编译一个OS镜像，这个例子中，我们编译的是：core-iamge-sato

    $bitbake core-image-sato

### 使用QEMU模拟镜像：Simulate Your Image Using QEMU

一旦镜像编译完成，就可以启动QEMU。（QEMU是可一个和Yocto 项目一起的快速模拟器）

    $runqemu qemux86

如果像学习更多的QEMU，可以查看（ the Yocto Project Development Tasks Manual）如下内容： "[Using the Quick EMUlator (QEMU)](http://www.yoctoproject.org/docs/2.5/dev-manual/dev-manual.html#dev-manual-qemu)".

### 退出QEMU：Exit QEMU

点击QEMU窗口的关闭按钮或 Ctrl-C就可以退出QEMU。

## 为特定硬件定制编译：Customizing Your Build for Specific Hardware

一般来说，layers是包含相关指令集和配置的库，他告诉Yocto Project要做什么。将相关的元数据隔离在功能特定的层中有助于模块化开发，并更容易重用层中的元数据。

    方便起见，layers通常以“meta-”命名。

按照如下步骤可以新增一个硬件层。

### 找一个Layer：Find a Layer

Yocto Project的[源码库](http://git.yoctoproject.org/)中有很多硬件层.当前以[meta-altera](https://github.com/kraj/meta-altera)为例。

### 复制这个Layer：Clone the Layer

通过git命令，将这个git库clone到本地。放在之前poky库的顶层。

     $ cd ~/poky
     $ git clone https://github.com/kraj/meta-altera.git
     Cloning into 'meta-altera'...
     remote: Counting objects: 25170, done.
     remote: Compressing objects: 100% (350/350), done.
     remote: Total 25170 (delta 645), reused 719 (delta 538), pack-reused 24219
     Receiving objects: 100% (25170/25170), 41.02 MiB | 1.64 MiB/s, done.
     Resolving deltas: 100% (13385/13385), done.
     Checking connectivity... done.

这时，这个硬件层，就和其他层一样存在编译机器的Poky库下面了，并且包含了altera硬件所需要的全部元数据。Altera是归Intel所有的。

### 为特定机器配置编译：Change the Configuration to Build for a Specific Machine

local.conf文件中的“MACHINE”变量,指定了编译生成的机器。这个例子中，“MACHINE”是"cyclones“。现在这个配置在：conf/machine/cyclone5.conf.

### 将你的层添加到层配置文件中：Add Your Layer to the Layer Configuration File

在编译过程你使用一个层之前，必须将这个层添加到构建路径的bblayers.conf文件中。

使用**bitbake-layers add-layer**命令将这个layer添加到配置文件中。

     $ cd ~/poky/build
     $ bitbake-layers add-layer ../meta-altera
     NOTE: Starting bitbake server...
     Parsing recipes: 100% |##################################################################| Time: 0:00:32
     Parsing of 918 .bb files complete (0 cached, 918 parsed). 1401 targets, 123 skipped, 0 masked, 0 errors.

添加layer的详细信息在如下位置：[Adding a Layer Using the bitbake-layers Script](http://www.yoctoproject.org/docs/2.5/dev-manual/dev-manual.html#adding-a-layer-using-the-bitbake-layers-script)

按照这些步骤完成之后，你当前的Yocto Project开发环境中就添加了meta-altera层，并可以执行cyclone5的编译。

查看Altera的README可以帮助你编译一个cyclone5的image。

## 创建自己的Layer：Creating Your Own General Layer

也许你有一个应用程序或者你需要隔离一组特定的行为，你可以使用**bitbake-layers create-layer**来创建自己的通用层。这个工具自动创建一个包含layer.conf配置文件的目录，这个recipes-example子目录里面包含example.bb recipes文件，licensing文件和README。

命令如下：

     $cd ~/poky
     $bitbake-layers create-layer meta-mylayer
     NOTE: Starting bitbake server...
     Add your new layer with 'bitbake-layers add-layer meta-mylayer'

更多详细信息可以参考[Creating a General Layer Using the bitbake-layers Script](http://www.yoctoproject.org/docs/2.5/dev-manual/dev-manual.html#creating-a-general-layer-using-the-bitbake-layers-script)
