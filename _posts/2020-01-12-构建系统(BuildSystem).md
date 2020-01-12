---
layout: mypost
title: 构建系统(BuildSystem)
categories: [构建系统,BuildSystem]
---

# 构建系统(BuildSystem)

构建系统(build system)是用来从源代码生成用户可以使用的目标(targets)的自动化工具。

目标可以包括库、可执行文件、或者生成的脚本等等。

常用的构建系统包括GNU Make、GNU autotools、CMake、Apache Ant（主要用于JAVA）。

此外，所有的集成开发环境（IDE）比如Qt Creator、Microsoft Visual Studio和Eclipse都对他们支持的语言添加了自己的构建系统配置工具。

通常IDE中的构建系统只是基于控制台的构建系统（比如Autotool和CMake）的前端。