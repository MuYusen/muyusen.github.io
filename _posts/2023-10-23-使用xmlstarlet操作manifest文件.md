---
layout: mypost
title: 使用xmlstarlet操作manifest文件
categories: [xmlstarlet,manifest]
---

## 介绍

XMLStarlet是一组命令行实用程序（工具），可用于使用简单的shell命令集对XML文档和文件进行转换、查询、验证和编辑，其方式类似于使用UNIX grep、sed、awk、diff、patch、join等命令对纯文本文件进行操作。这组命令行实用程序可供在UNIX shell命令提示符上处理许多XML文档的人员使用，也可用于使用shell脚本来进行自动XML处理。

## 命令概要

```bash
xmlstarlet [options...] {command} [cmd-options...]
```

`command` 使用:

+ ed    (或 edit)      - 编辑或更新 XML 文档
+ sel   (或 select)    - 选择数据或查询 XML 文档（使用 XPATH 等）
+ tr    (或 transform) - 使用 XSLT 转换 XML 文档
+ val   (或 validate)  - 验证 XML 文档（有效/DTD/XSD/RelaxNG）
+ fo    (或 format)    - 格式化 XML 文档
+ el    (或 elements)  - 显示 XML 文档的元素结构
+ c14n  (或 canonic)   - XML 规范化
+ ls    (或 list)      - 以 XML 格式列出目录
+ esc   (或 escape)    - 转义特殊 XML 字符
+ unesc (或 unescape)  - 取消转义特殊 XML 字符
+ pyx   (或 xmln)      - 将 XML 转换为 PYX 格式（基于 ESIS - ISO 8879）
+ p2x   (或 depyx)     - 将 PYX 转换为 XML

## 使用xmlstarlet操作AGL静态manifest文件

文件格式如下：

[manifests.xml](manifests.xml)

```bash
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
  <remote name="agl" fetch="https://gerrit.automotivelinux.org/gerrit/" pushurl="ssh://gerrit.automotivelinux.org:29418" review="https://gerrit.automotivelinux.org/gerrit/"/>
  <remote name="github" fetch="https://github.com/"/>
  <remote name="openembedded" fetch="https://git.openembedded.org/"/>
  <remote name="yocto" fetch="https://git.yoctoproject.org/git/"/>
  
  <default remote="agl" revision="master" sync-j="4"/>
  
  <project name="AGL/meta-agl" path="meta-agl" revision="f399150c4e6bc9de6172cb0c45a130edf10643dc" upstream="master" dest-branch="master"/>
  <project name="AGL/meta-agl-demo" path="meta-agl-demo" revision="2506ab2bfcdaa53f2faa9221324f9fee22c9f6d9" upstream="master" dest-branch="master"/>
  <project name="AGL/meta-agl-devel" path="meta-agl-devel" revision="0afcc8915999c48f2f8886887b2e9c33138f55fd" upstream="master" dest-branch="master"/>
  <project name="AGL/meta-agl-refhw" path="bsp/meta-agl-refhw" revision="36ef6304391d09979b23664220d593c79dc5cb50" upstream="master" dest-branch="master"/>
  <project name="CogentEmbedded/meta-rcar" path="bsp/meta-rcar" remote="github" revision="511808a3e794ad0e35386f83d3d159c9ba48b4be" upstream="kirkstone-Yocto-v5.9.0"/>
  <project name="EmbeddedAndroid/meta-rtlwifi" path="bsp/meta-rtlwifi" remote="github" revision="032a394e7569d1254cd17a0358475b986e64a5a4" upstream="master"/>
  <project name="Freescale/meta-freescale-3rdparty" path="bsp/meta-freescale-3rdparty" remote="github" revision="6dbdabe09e410818dcda8801e1cbd8b68d539e63" upstream="kirkstone"/>
  <project name="SanCloudLtd/meta-sancloud" path="bsp/meta-sancloud" remote="github" revision="e1a23019ce2ff3879986fa63071f1a2caa23697c" upstream="kirkstone"/>
  <project name="dl9pf/meta-codechecker" path="external/meta-codechecker" remote="github" revision="f27a46feb2291d333744850a82d5c8af303e3bd5" upstream="master"/>
  <project name="dl9pf/meta-sifive" path="bsp/meta-sifive" remote="github" revision="80a979fd222623b56e44493cf92bd9e276a65453" upstream="master"/>
  <project name="kraj/meta-clang" path="external/meta-clang" remote="github" revision="2d08d6bf376a1e06c53164fd6283b03ec2309da4" upstream="kirkstone"/>
  <project name="meta-arm" path="bsp/meta-arm" remote="yocto" revision="b187fb9232ca0a6b5f8f90b4715958546fc41d73" upstream="kirkstone"/>
  <project name="meta-flutter/meta-flutter" path="external/meta-flutter" remote="github" revision="eb4ba236a09b448d048b21a89c3ad22d12839569" upstream="kirkstone"/>
  <project name="meta-flutter/workspace-automation" path="external/workspace-automation" remote="github" revision="06e057c07e12d3a403036926ae01c8da33f225c4" upstream="main"/>
  <project name="meta-freescale" path="bsp/meta-freescale" remote="yocto" revision="3e9ef23d98aa842cf84251a27c9b8dde8925ea61" upstream="kirkstone"/>
  <project name="meta-lts-mixins" path="external/meta-lts-mixins_rust-1.68" remote="yocto" revision="59fd1bd537f501f93df7d1e46a103bf8f2195ed3" upstream="kirkstone/rust-1.68"/>
  <project name="meta-python2" path="external/meta-python2" remote="openembedded" revision="f02882e2aa9279ca7becca8d0cedbffe88b5a253" upstream="kirkstone"/>
  <project name="meta-qt5/meta-qt5" path="external/meta-qt5" remote="github" revision="bff5bd937f0776166e81a63f3dd39ede348ef758" upstream="kirkstone"/>
  <project name="meta-raspberrypi" path="bsp/meta-raspberrypi" remote="yocto" revision="80a12f7fddfeae28c43242765374e7ade3a2a59e" upstream="kirkstone"/>
  <project name="meta-security" path="external/meta-security" remote="yocto" revision="06c240c3c235fa06fe724e278d53463ac67cc26b" upstream="kirkstone"/>
  <project name="meta-selinux" path="external/meta-selinux" remote="yocto" revision="a401f4b2816a0b41ce8d9351542658c721935bcd" upstream="kirkstone"/>
  <project name="meta-spdxscanner" path="external/meta-spdxscanner" remote="yocto" revision="25373bc58ece8b6f07680a103b4a1bbbc99e0db1" upstream="master"/>
  <project name="meta-tensorflow" path="external/meta-tensorflow" remote="yocto" revision="ec20e19319f9eb89ceadc04923cc5bc75e865692" upstream="kirkstone"/>
  <project name="meta-ti" path="bsp/meta-ti" remote="yocto" revision="1ccba22923b1c0ce3558075f3de3846ba983155c" upstream="kirkstone"/>
  <project name="meta-virtualization" path="external/meta-virtualization" remote="yocto" revision="e60f59f3567b78286ea34337dbae468a5f18f1c6" upstream="kirkstone"/>
  <project name="openembedded/meta-openembedded" path="external/meta-openembedded" remote="github" revision="529620141e773080a6a7be4615fb7993204af883" upstream="kirkstone"/>
  <project name="poky" path="external/poky" remote="yocto" revision="d6b8790370500b99ca11f0d8a05c39b661ab2ba6" upstream="kirkstone"/>
  <project name="renesas-rcar/meta-renesas" path="bsp/meta-renesas" remote="github" revision="f669cd36bd9dbdec6d8d9a85d5ca164180973706" upstream="kirkstone-dev"/>
</manifest>
```

## xmlstarlet sel

```bash
xmlstarlet sel --help
```

+ 取出所有`project`的`name`和``path

```bash
xmlstarlet sel -t -m "/manifest/project" -v "string(@name)" -o " " -v  "string(@path)"  -nl manifests.xml
```

+ 取出`name='renesas-rcar/meta-renesas'` 的 `project` 的 `revision`

```bash
xmlstarlet sel -t -m "/manifest/project[@name='renesas-rcar/meta-renesas']" -v "string(@revision)"  -nl manifests.xml
```

## xmlstarlet ed

```bash
xmlstarlet ed --help
```

+ 修改 `project`中`name='renesas-rcar/meta-renesas'` 的 `revision` 修改为 `123`

```bash
xmlstarlet ed -L -u "/manifest/project[@name='renesas-rcar/meta-renesas']/@revision" -v "123"   manifests.xml
```
