---
layout: mypost
title: Jenkins中使用cppcheck进行C代码检查
categories: [Jenkins]
---

## Jenkins中使用cppcheck进行C代码检查

cppcheck 是一个静态代码检查工具，支持c, c++ 代码；作为编译器的一种补充检查，cppcheck对产品的源代码执行严格的逻辑检查。 执行的检查包括：

1. 自动变量检查
2. 数组的边界检查
3. class类检查
4. 过期的函数，废弃函数调用检查
5. 异常内存使用，释放检查
6. 内存泄漏检查，主要是通过内存引用指针
7. 操作系统资源释放检查，中断，文件描述符等
8. 异常STL 函数使用检查
9. 代码格式错误，以及性能因素检查

### Jenkins中安装Cppcheck插件

以管理员账号登录Jenkins，进入“系统管理”——“管理插件”——“可用插件”，中找到Cppcheck这款插件，选择安装。

### Jenkins slave中安装Cppcheck

在每台Jenkins slave中安装cppcheck工具

```
~$ sudo apt-get install cppcheck
```

说明：Jenkins 中的cppcheck插件，只用做处理展示cppcheck分析结果，真正进行分析的是每台slave上的cppcheck工具

### Jenkins Job的配置。

以github上的bazel代码为例：git@github.com:bazelbuild/bazel.git

#### 新建Job：cppcheck_bazel
#### 下载代码：

![输入图片说明](27200353_9VuT.png)
```   
    下载git@github.com:bazelbuild/bazel.git代码的master分支，clone到${WORKSPACE}/code目录下面
```
#### 执行cppcheck进行分析：

    扫描code路径下的代码，并生成cppcheck.xml扫描结果文件；将代码路径换成文件列表（test.list）,可以扫描分析指定文件。

```
#!/bin/bash

cd ${WORKSPACE}

cppcheck -j 4 --enable=all --inconclusive  --xml --xml-version=2 code 2> cppcheck.xml

#cppcheck -j 4 --enable=all --inconclusive  --xml --xml-version=2 --file-list=test.list 2> cppcheck.xml

```

#### 构建后操作

![输入图片说明](27200419_gnGy.png)

在“构建后操作”中，选择“Publish Cppcheck results”,在Cppcheck report XMLs中填入我们在构建操作中生成的结果文件（cppcheck.xml）

#### 点击“立即构建”，进行cppcheck分析。