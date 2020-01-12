---
layout: mypost
title: Linux命令优先级 —— alias、function、内部命令(make)
categories: [Linux,Make]
---
# Linux 命令优先级 —— alias、function、内部命令(make)

## make

+ 新建Makefile文件，在里面输入如下内容；

```bash
# Makefile内容
all:
@echo "Make Command echo : Hello "
```

+ 保存，执行 which make ，查看make命令的位置

```bash
$ which make
/usr/bin/make
```

+ 执行make，查看输出：

```bash
$ make
Make Command echo : Hello

```

## function make

+ 新建文件envsetup.sh，在里面输入如下内容：

```bash
#!/bin/bash

function make(){
    echo "Function Make echo : Hello"
    make

    exit
}

```

+ 执行 source envsetup.sh

+ 执行make

```bash
$ make
Function Make echo : Hello
Function Make echo : Hello

……
```

**此时可以看到，死循环了，在function make 里面调用的还是function make**

## alias make 

+ 在~/bin/下新建makex，输入如下内容：

```bash
$ cat ~/bin/makex
#!/bin/bash

echo "Alias Make echo : Hello"

```

+ 设置别名：alias make=~/bin/makex

+ 执行make

```bash
$ make
Alias Make echo : Hello
```

**说明：alias make 的优先级高于command make**

+ 修改～/bin/makex 文件

```bash
#!/bin/bash

echo "Alias Make echo : Hello"
make
```

+ 执行make

```bash
$ make
Alias Make echo : Hello
Make Command echo : Hello

```

**alias make 中可以直接调用内部命令make**

+ 修改~/bin/makex

```bash
$ cat ~/bin/makex

#!/bin/bash

function make(){
    echo "Function Makex echo : Hello"
    command make
}

echo "Alias Make echo : Hello"
make

```

+ 执行make

```bash
$ make
Alias Make echo : Hello
Function Makex echo : Hello
Make Command echo : Hello

```

**优先级：alias > function make > 内部命令make**

## function make 和 command make

+ 修改envsetup.sh文件

```bash
$ cat envsetup.sh
#!/bin/bash

function make(){
    echo "Function Make echo : Hello"
    command make
}

```

+ source envsetup.sh

+ 执行make

```bash
$ make
Function Make echo : Hello
Make Command echo : Hello

```

**结果：**
**1、fucntion make 的优先级高于command make**
**2、在function 中可以调用内部make命令，但是要加上command**