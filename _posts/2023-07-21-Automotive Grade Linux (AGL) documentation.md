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

### 初始化编译环境

#### `aglsetup.sh` Script

    $AGL_TOP/octopus/meta-agl/scripts/aglsetup.sh

该脚本接受许多选项，这些选项允许您定义构建参数，例如目标硬件（即机器）、构建目录等。

#### AGL Machines (board support)

通过`-m`参数选择目标平台。MACHINE存在位置`meta-agl/templates/machine/*`,`这里也是添加新板的位置`。

Example:

    Available machines:
       [meta-agl]
           bbe
           beaglebone
           cubox-i
           ebisu
           generic-arm64
           h3-salvator-x
           h3ulcb
           h3ulcb-kf
           h3ulcb-nogfx
           imx6qdlsabreauto
           imx8mq-evk
           imx8mq-evk-viv
           intel-corei7-64
           j721e-evm
           m3-salvator-x
           m3ulcb
           m3ulcb-kf
           m3ulcb-nogfx
           qemuarm
           qemuarm64
           qemuriscv64
         * qemux86-64
           raspberrypi4
           virtio-aarch64

#### AGL Features

你希望镜像中包含哪些AGL Features。

Example:

    Available features:
       [meta-agl]
           agl-all-features :( agl-demo  agl-pipewire  agl-app-framework  agl-selinux  agl-flutter  agl-pipewire  agl-app-framework  agl-netboot )
           agl-app-framework
           agl-archiver
           agl-buildstats
           agl-ci :( agl-create-spdx )
           agl-ci-change-features :( agl-demo  agl-pipewire  agl-app-framework  agl-selinux  agl-flutter  agl-pipewire  agl-app-framework  agl-devel  agl-package-management  agl-netboot  agl-pipewire  agl-buildstats  agl-ptest )
           agl-ci-change-features-nogfx :( agl-demo  agl-pipewire  agl-app-framework  agl-selinux  agl-flutter  agl-pipewire  agl-app-framework  agl-devel  agl-package-management  agl-netboot  agl-pipewire  agl-buildstats  agl-ptest )
           agl-ci-snapshot-features :( agl-demo  agl-pipewire  agl-app-framework  agl-selinux  agl-flutter  agl-pipewire  agl-app-framework  agl-devel  agl-package-management  agl-netboot  agl-archiver  agl-pipewire  agl-buildstats  agl-ptest )
           agl-ci-snapshot-features-nogfx :( agl-demo  agl-pipewire  agl-app-framework  agl-selinux  agl-flutter  agl-pipewire  agl-app-framework  agl-devel  agl-package-management  agl-netboot  agl-archiver  agl-pipewire  agl-buildstats  agl-ptest )
           agl-create-spdx
           agl-devel :( agl-package-management )
           agl-fossdriver
           agl-localdev
           agl-netboot
           agl-package-management
           agl-pipewire
           agl-ptest
           agl-refhw-h3
           agl-selinux
           agl-virt
           agl-virt-guest-xen
           agl-virt-xen :( agl-virt )
           agl-weston-remoting :( agl-demo  agl-pipewire  agl-app-framework  agl-selinux  agl-flutter  agl-pipewire  agl-app-framework )
       [meta-agl-demo]
           agl-container-guest-demo :( agl-demo  agl-pipewire  agl-app-framework  agl-selinux  agl-flutter  agl-pipewire  agl-app-framework  agl-drm-lease )
           agl-demo :( agl-pipewire  agl-app-framework  agl-selinux  agl-flutter  agl-pipewire  agl-app-framework )
           agl-demo-cluster-support :( agl-weston-remoting  agl-demo  agl-pipewire  agl-app-framework  agl-selinux  agl-flutter  agl-pipewire  agl-app-framework )
           agl-demo-preload
           agl-kvm :( agl-demo  agl-pipewire  agl-app-framework  agl-selinux  agl-flutter  agl-pipewire  agl-app-framework )
       [meta-agl-devel]
           agl-basesystem
           agl-cef
           agl-drm-lease
           agl-egvirt
           agl-flutter :( agl-pipewire  agl-app-framework )
           agl-ic-container :( agl-drm-lease  agl-pipewire  agl-selinux )
           agl-jailhouse
           agl-offline-voice-agent
           agl-test :( agl-ptest  agl-devel  agl-package-management )

#### AGL 编译命令示例

    $ source meta-agl/scripts/aglsetup.sh -m qemux86-64 -b qemux86-64 agl-demo agl-devel
    aglsetup.sh: Starting
    Generating configuration files:
       Build dir: /home/scottrif/workspace_agl/build
       Machine: qemux86-64
       Features: agl-appfw-smack agl-demo agl-devel
       Running /home/scottrif/workspace_agl/poky/oe-init-build-env
       Templates dir: /home/scottrif/workspace_agl/meta-agl/templates/base
       Config: /home/scottrif/workspace_agl/build/conf/bblayers.conf
       Config: /home/scottrif/workspace_agl/build/conf/local.conf
       Setup script: /home/scottrif/workspace_agl/build/conf/setup.sh
       Executing setup script ... --- beginning of setup script
     fragment /home/scottrif/workspace_agl/meta-agl/templates/base/01_setup_EULAfunc.sh
     fragment /home/scottrif/workspace_agl/meta-agl/templates/base/99_setup_EULAconf.sh
     end of setup script
    OK
    Generating setup file: /home/scottrif/workspace_agl/build/agl-init-build-env ... OK
    aglsetup.sh: Done
     Shell environment set up for builds.
    You can now run 'bitbake target'
    Common targets are:
      - meta-agl:          (core system)
        agl-image-minimal
        agl-image-minimal-qa

        agl-image-ivi
        agl-image-ivi-qa
        agl-image-ivi-crosssdk

        agl-image-weston

      - meta-agl-demo:     (demo with UI)
        agl-demo-platform  (* default demo target)
        agl-demo-platform-qa
        agl-demo-platform-crosssdk
        agl-demo-platform-html5

+ 编译目录：默认`$AGL_TOP/<release-branch-name>/build`
+ 生成主要配置文件：`local.conf` ，`bblayers.conf`，编译目录的conf下面

### 自定义编译参数

修改`$AGL_TOP/<release-branch-name>/<build-dir>/conf/local.conf`文件中的编译构建参数。同Yocto项目。

#### Capturing Build History

    INHERIT += "buildhistory"
    BUILDHISTORY_COMMIT = "1"

#### Deleting Temporary Workspace

    INHERIT += "rm_work"

#### Pointing at Shared State Cache Locations

[参考](https://www.yoctoproject.org/docs/3.1.4/ref-manual/ref-manual.html#shared-state-cache)

    SSTATE_DIR = "${AGL_TOP}/sstate-cache"

    SSTATE_MIRRORS ?= "\
        file://.* http://someserver.tld/share/sstate/PATH;downloadfilename=PATH \n \
        file://.* file:///some/local/dir/sstate/PATH"

#### Preserving the Download Directory

默认的下载路径是`downloads`.

    DL_DIR = "${AGL_TOP}/downloads"

#### Using a Shared State (sstate) Mirror

[参考](https://yoctoproject.org/docs/3.1.4/ref-manual/ref-manual.html#shared-state-cache)

    SSTATE_MIRRORS_append = " file://.* https://download.automotivelinux.org/sstate-mirror/octopus/${DEFAULTTUNE}/PATH \n "

#### Common Settings using Symbolic Link with site.conf

Example:

    $ echo "# reuse download directories" >> $AGL_TOP/site.conf
    $ echo "DL_DIR = \"$HOME/downloads/\"" >> $AGL_TOP/site.conf
    $ echo "SSTATE_DIR = \"$AGL_TOP/sstate-cache/\"" >> $AGL_TOP/site.conf
    $ cd $AGL_TOP/octopus/qemux86-64/
    $ ln -sf $AGL_TOP/site.conf conf/

    In General;
    $ cd $AGL_TOP/<release-branch-name>/<build-dir>/
    $ ln -sf $AGL_TOP/site.conf conf/
