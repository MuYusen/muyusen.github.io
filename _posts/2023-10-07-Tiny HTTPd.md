---
layout: mypost
title: Tiny HTTPd
categories: [TinyHTTPd]
---

## Tiny HTTPd

（最近在学习Valgrind，所以找一些C/C++项目，编译一下看看效果）

tinyhttpd是一个超轻量型Http Server，使用C语言开发，全部代码只有 502 行（包括注释），附带一个简单的 Client

可以通过阅读这段代码理解一个 Http Server 的本质。

项目地址：http://sourceforge.net/projects/tinyhttpd/

### 编译 （make all）

`编译准备`

```bash
/* This program compiles for Sparc Solaris 2.6.
 * To compile for Linux:
 *  1) Comment out the #include <pthread.h> line.
 *  2) Comment out the line that defines the variable newthread.
 *  3) Comment out the two lines that run pthread_create().
 *  4) Uncomment the line that runs accept_request().
 *  5) Remove -lsocket from the Makefile.
 */
```

`查看Makefile文件`

```bash
all: httpd

httpd: httpd.c
        gcc -W -Wall -lsocket -lpthread -o httpd httpd.c

clean:
        rm httpd
```

`编译完成之后运行 echo $? , 返回 0 ，编译成功。`

### 运行

```bash
10169 ◯ : ./httpd                                                                                                                                                                                              [~]
httpd running on port 59253

```

浏览器打开：http://localhost:59253/ , 如下显示，`成功`.

![tinyhttpd](2023-10-07_14-56.png)
