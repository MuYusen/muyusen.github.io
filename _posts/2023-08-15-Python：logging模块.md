---
layout: mypost
title: Python:logging模块
categories: [Python,logging]
---

今天面试时被问到的，之前了解不深，写一下。

logging模块是Python内置的标准模块，主要用于输出运行日志，可以设置输出日志的等级、日志保存路径、日志文件回滚等；相比print，具备如下优点：

+ 可以通过设置不同的日志等级，在release版本中只输出重要信息，而不必显示大量的调试信息；
+ print将所有信息都输出到标准输出中，严重影响开发者从标准输出中查看其它数据；logging则可以由开发者决定将信息输出到什么地方，以及怎么输出；

Logger从来不直接实例化，经常通过logging模块级方法 logging.getLogger(name)来获得，其中如果name不给定就用root。名字是以点号分割的命名方式命名的(a.b.c)。对同一个名字的多个调用logging.getLogger()方法会返回同一个logger对象。这种命名方式里面，后面的loggers是前面logger的子logger，自动继承父loggers的log信息，正因为此,没有必要把一个应用的所有logger都配置一遍，只要把顶层的logger配置好了，然后子logger根据需要继承就行了。

logging.Logger对象扮演了三重角色:

+ 首先,它暴露给应用几个方法以便应用可以在运行时写log.
+ 其次,Logger对象按照log信息的严重程度或者根据filter对象来决定如何处理log信息(默认的过滤功能).
+ 最后,logger还负责把log信息传送给相关的handlers.

## 基本使用

```bash
# 配置
import logging
logging.basicConfig(level = logging.INFO,format = '%(asctime)s - %(name)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)

# 使用
logger.info("Start print log")
logger.debug("Do something")
logger.warning("Something maybe fail.")
logger.info("Finish")
```

输出

```bash
2023-08-15 15:28:23,881 - __main__ - INFO - Start print log
2023-08-15 15:28:23,882 - __main__ - WARNING - Something maybe fail.
2023-08-15 15:28:23,882 - __main__ - INFO - Finish
```

logging中可以选择很多消息级别，如：DEBUG，INFO，WARNING，ERROR，CRITICAL，通过赋予logger或者handler不同的级别，开发者就可以只输出错误信息到特定的记录文件，或者在调试时只记录调试信息。

将logger的级别改为DEBUG，再观察一下输出结果:

```bash
logging.basicConfig(level = logging.DEBUG,format = '%(asctime)s - %(name)s - %(levelname)s - %(message)s')
```

输出了debug的日志记录

```bash
2023-08-15 15:30:38,137 - __main__ - INFO - Start print log
2023-08-15 15:30:38,137 - __main__ - DEBUG - Do something
2023-08-15 15:30:38,137 - __main__ - WARNING - Something maybe fail.
2023-08-15 15:30:38,137 - __main__ - INFO - Finish
```

logging.basicConfig函数各参数：

+ filename：指定日志文件名；
+ filemode：和file函数意义相同，指定日志文件的打开模式，'w'或者'a'；
+ format：指定输出的格式和内容，format可以输出很多有用的信息，
+ datefmt：指定时间格式，同time.strftime()；
+ level：设置日志级别，默认为logging.WARNNING；
+ stream：指定将日志的输出流，可以指定输出到sys.stderr，sys.stdout或者文件，默认输出到sys.stderr，当stream和filename同时指定时，stream被忽略;

| 属性名称 | 格式  | 说明   |
|  ----  | ----  | ----  |
| name | %(name)s | 日志的名称 |
| asctime |%(asctime)s | 可读时间，默认格式‘2003-07-08 16:49:45,896'，逗号之后是毫秒 |
| filename |%(filename)s | 文件名，pathname的一部分 |
| pathname |%(pathname)s | 文件的全路径名称 |
| funcName | %(funcName)s | 调用日志多对应的方法名 |
| levelname | %(levelname)s  | 日志的等级 |
| levelno  | %(levelno)s | 数字化的日志等级 |
| lineno  | %(lineno)d  | 被记录日志在源码中的行数 |
| module | %(module)s | 模块名 |
| msecs | %(msecs)d | 时间中的毫秒部分 |
| process  | %(process)d | 进程的ID |
| processName | %(processName)s | 进程的名称 |
| thread | %(thread)d | 线程的ID |
| threadName | %(threadName)s | 线程的名称 |
| relativeCreated | %(relativeCreated)d | 日志被创建的相对时间，以毫秒为单位 |

## 日志写入文件

设置logging，创建一个FileHandler，并对输出消息的格式进行设置，将其添加到logger，然后将日志写入到指定的文件中，

```bash
# 配置
import logging
logger = logging.getLogger(__name__)
logger.setLevel(level = logging.INFO)
handler = logging.FileHandler("log.txt")
handler.setLevel(logging.INFO)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)
logger.addHandler(handler)

# 使用
logger.info("Start print log")
logger.debug("Do something")
logger.warning("Something maybe fail.")
logger.info("Finish")

```

## 同时输出到屏幕和日志文件

logger中添加StreamHandler，可以将日志输出到屏幕上

```bash
import logging
logger = logging.getLogger(__name__)
logger.setLevel(level = logging.INFO)
handler = logging.FileHandler("log.txt")
handler.setLevel(logging.INFO)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)
console = logging.StreamHandler()
console.setLevel(logging.INFO)

logger.addHandler(handler)
logger.addHandler(console)

logger.info("Start print log")
logger.debug("Do something")
logger.warning("Something maybe fail.")
logger.info("Finish")
```
