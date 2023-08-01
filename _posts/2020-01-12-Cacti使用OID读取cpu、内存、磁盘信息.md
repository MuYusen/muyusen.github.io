---
layout: mypost
title: Cacti使用OID读取cpu、内存、磁盘信息
categories: [Cacti]
---

SNMP指的是简单网路管理协议（Simple Network Management Protocol），它主要包含三个部分：被管理的设备、代理、网络管理站。一个被管理设备是具有SNMP代理的一个网络节点，可以是路由器、交换机、主机、打印机等设备，通常代理是设备内部的一个进程，网络管理站通过此进程和设备通信。

MIB指的是管理信息库（Management Information Base），就是代理进程所包含的的能被管理站查询和设置的信息的集合，具体内容在RFC1213中定义。

OID是一个对象标示符，通过OID可以唯一的标示一个MIB层次结构中的被管理对象，在SNMP中我们使用OID来获取所需要的信息，例如我们使用如下名利可以获取计算机名称：

    snmpwalk -v 2c -c public 127.0.0.1 .1.3.6.1.2.1.1.5

如果想要通过snmp获取计算机的cpu、内存、硬盘等信息，就要首先在机器上安装snmp服务，然后通过snmp客户端获取需要的信息。以下是获取cpu、内存、硬盘信息时所需要的OID

## CPU

```bash

负载
1 minute Load: .1.3.6.1.4.1.2021.10.1.3.1
5 minute Load: .1.3.6.1.4.1.2021.10.1.3.2
15 minute Load: .1.3.6.1.4.1.2021.10.1.3.3


CPU使用
percentage of user CPU time: .1.3.6.1.4.1.2021.11.9.0
raw user cpu time: .1.3.6.1.4.1.2021.11.50.0
percentages of system CPU time: .1.3.6.1.4.1.2021.11.10.0
raw system cpu time: .1.3.6.1.4.1.2021.11.52.0
percentages of idle CPU time: .1.3.6.1.4.1.2021.11.11.0
raw idle cpu time: .1.3.6.1.4.1.2021.11.53.0
raw nice cpu time: .1.3.6.1.4.1.2021.11.51.0
```

## 内存信息

```bash
Total Swap Size: .1.3.6.1.4.1.2021.4.3.0
Available Swap Space: .1.3.6.1.4.1.2021.4.4.0
Total RAM in machine: .1.3.6.1.4.1.2021.4.5.0
Total RAM used: .1.3.6.1.4.1.2021.4.6.0
Total RAM Free: .1.3.6.1.4.1.2021.4.11.0
Total RAM Shared: .1.3.6.1.4.1.2021.4.13.0
Total RAM Buffered: .1.3.6.1.4.1.2021.4.14.0
Total Cached Memory: .1.3.6.1.4.1.2021.4.15.0
```

## 硬盘信息

要获取硬盘信息，需要在snmpd.conf中加入以下信息（假设硬盘只有一个根分区(/)）：Disk / 100000

```bash
Path where the disk is mounted: .1.3.6.1.4.1.2021.9.1.2.1
Path of the device for the partition: .1.3.6.1.4.1.2021.9.1.3.1
Total size of the disk/partion (kBytes): .1.3.6.1.4.1.2021.9.1.6.1
Available space on the disk: .1.3.6.1.4.1.2021.9.1.7.1
Used space on the disk: .1.3.6.1.4.1.2021.9.1.8.1
Percentage of space used on disk: .1.3.6.1.4.1.2021.9.1.9.1
Percentage of inodes used on disk: .1.3.6.1.4.1.2021.9.1.10.1
System Uptime: .1.3.6.1.2.1.1.3.0
```
