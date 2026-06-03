layout: mypost
title: nvidia drive os  系统软件工具（copytarget & build-fs）
categories: [nvidia,copytarget,build-fs]
---

## nvidia drive os  系统软件工具（copytarget & build-fs）

## [copytarget](https://developer.nvidia.com/docs/drive/drive-os/6.0.5/public/drive-os-linux-sdk/common/topics/sys_components/CopyTarget1.html)

The CopyTarget tool copies files reliably from a source location to the destination location while maintaining file metadata, which includes ownership and permission.

CopyTarget supports the following operations:

+ Copy file
+ Create directory
+ Update file metadata
+ Update directory metadata
+ Remove file
+ Remove empty directory
+ Create symlink

Prerequisites

+ Ubuntu 18.04 x86_64 host
+ python3 (>= 3.5)
+ python3-yaml
+ coreutils
+ bash

## [build-fs](https://developer.nvidia.com/docs/drive/drive-os/6.0.5/public/drive-os-linux-sdk/common/topics/sys_components/NVIDIABuild-Kit1.html)

NVIDIA Build-FS is a tool for creating and customizing Linux filesystems. It is a common filesystem interface tool for automotive programs.

`参考文档的版本是6.*.*版本的，现在应该7.*.*的了`
