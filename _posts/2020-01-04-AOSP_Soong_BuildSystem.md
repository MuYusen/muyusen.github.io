---
layout: mypost
title: AOSP_Soong_BuildSystem
categories: [AOSP,Soong]
---
# Android Soong BuildSystem

## 一 概述

### 1.1 编译系统变化

     随着android工程越来越大，包含的module越来越多，以makefile组织的项目编译花费的时间越来越多。谷歌在7.0开始引入了ninja进行编译系统的组织。相对于make来说ninja在大的项目管理中速度和并行方面有突出的优势，因此谷歌采用了ninja来取代之前使用的make。但是现有的android项目还是由makefile组织，因此谷歌引入了kati将makefile翻译成ninja文件。

   从Android 7.0开始，android的编译系统发生了变化，之前依赖Makefile组织编译系统，从7.0开始逐步引入了kati soong(optional未正式使用，需要USE_SOONG=true开启)，将Android.mk文件转化成ninja文件，使用ninja文件对编译系统进行管理。

从8.0开始，android 引入了Android.bp文件来替代之前的Android.mk文件，不同于Android.mk，Android.bp只是纯粹的配置文件，不包括分支、循环等流程控制。在android项目上如何进行选择编译、解析配置、转换成ninja等，Soong就被创造出来，将Android.bp转换为ninja文件进行管理。

同时，Soong还会编译长生一个androidmk命令可以手动将Android.mk转换成Android.bp文件。

### 1.2 代码位置

Kati的位置是在build/kati/中，平台也自带编译好的ckati

Soong的位置在build/soong,它和build/blueprint同时起作用，他们之间的系统关系如下：

![关系](10408596-1abc7f4c7d2e19ed.png)

Blueprint更像是一个库，专门来翻译blueprint文件，关于Blueprint文件格式可以参考build/blueprint/Blueprints文件，soong是在blueprint上面的扩展，基于blueprint的语法定制产生Android.bp语法，解析Android.bp文件生成ninja文件。

Blueprint和Soong都是有Go语言写的项目，从7.0开始在prebuilts/go/目录下新增了go语言的运行环境，在编译是使用。

### 1.3 Android ninja组织

在编译过程中，将所有的android.bp文件搜集成out\soong\build.ninja.d , 并以此为基础生成out\soong\build.ninja规则。

由所有的Android.mk生成build-aosp_arm.ninja文件。通过combined-aosp_arm.ninja将两个文件组织起来。

builddir = out

include out/build-aosp_arm.ninja

include out/soong/build.ninja

build out/combined-aosp_arm.ninja: phony out/soong/build.ninja

通过编译的LOG，首先搜集所有的bp文件生成build.ninja，然后搜集所有的mk文件生成build-aosp-arm.ninja文件。

out/soong/.bootstrap/bin/soong_build out/soong/build.ninja

out/build-aosp_arm-cleanspec.ninja is missing, regenerating...

out/build-aosp_arm.ninja is missing, regenerating...

[1/894] including out/soong/Android-aosp_arm.mk ...

经过试验表明，soong处理后的bp文件生成build.ninja的同时还包含out/soong/Android-aosp_arm.mk，这个文件是编译完成后模块的安装脚本，负责将模块安装到对应位置。比如我们现在处理的vndk相关vendor处理就是在这个目录下进行的。

在out/soong目录下有两个文件.minibootstrap/build.ninja 和.bootstrap/build.ninja两个目录。.minibootstrap/build.ninja 主要是用来编译blueprint和生成.bootstrap/build.ninja。而.bootstrap/build.ninja主要是生成 soong相关工具和out/soong/build.ninja文件。

![编译](10408596-3a315dd0d7aaa517.png)

## 二 编译流程

### 2.1 make 流程

当我们在android 总目录下make时，默认寻找当前Makefile并找到all目标进行编译操作，这个是默认的make机制。当android更换为ninja是，在总目录下进行make时进行如何的流程呢。

同老的make机制一样，make找到总目录下的Makefile

```bash
$ cat Makefile

include build/make/core/main.mk

```

Makefile内容没有改变，同样是进入到main.mk中，刚开始几行有了明显的变化

```bash
ifndef KATI

host_prebuilts := linux-x86

ifeq ($(shell uname),Darwin)

host_prebuilts := darwin-x86

endif

.PHONY: run_soong_ui

run_soong_ui:

+@prebuilts/build-tools/$(host_prebuilts)/bin/makeparallel --ninja build/soong/soong_ui.bash --make-mode $(MAKECMDGOALS)

.PHONY: $(MAKECMDGOALS)

$(sort $(MAKECMDGOALS)) : run_soong_ui

@#empty

else # KATI

```

其中MAKECMDGOALS这个命名是make执行时后面的参数赋值，也就是我们执行任何make的时候都是执行run_soong_ui这个目标，这样android从Makefile切换为soong进行了编译流程，后面跟make就没有关系了。

### 2.2 mm流程

当我们单独编译某个模块时，在这个模块目录下输入mm命令进行编译，在build/envsetup.sh看一下具体实现过程：

```bash

function mm()
{

local T=$(gettop)

local DRV=$(getdriver $T)

# If we're sitting in the root of the build tree, just do a

# normal build.

if [ -f build/soong/soong_ui.bash ]; then

_wrap_build $DRV $T/build/soong/soong_ui.bash --make-mode $@

else

ONE_SHOT_MAKEFILE=$M _wrap_build $DRV $T/build/soong/soong_ui.bash --make-mode $MODULES $ARGS

```

流程上同make差不多只不过目标，是单独模块组成。

同时在mm一个单独模块是，生成独立的ninja文件 combined--aosp_arm-frameworks_native_libs_gui_Android.mk.ninja 和
build-aosp_arm-frameworks_native_libs_gui_Android.mk.ninja

### 2.3 Soong流程

在介绍soong编译流程之前，先介绍几个命令以便我们后续编译流程的理解，在out/soong/host/linux-x86/bin目录下，有几个命令是soong用到的，

+ soong_ui: soong编译的入口
+ androidmk: 后面的Android.bp用到，Android.mk一键转换为android.bp

在out/soong/.bootstrap/bin目录下有几个

```bash
out/soong/.bootstrap/bin$ ls

bpglob gotestmain gotestrunner loadplugins minibp soong_build soong_env
```

+ minibp： 在bootstrap是用到
+ soong_build ：这个很重要，主要是将bp文件转换为ninja文件

通过上文的分析，当我们make命令敲下去的时候，主要是执行

```bash
prebuilts/build-tools/$(host_prebuilts)/bin/makeparallel --ninja build/soong/soong_ui.bash --make-mode $(MAKECMDGOALS)
```

soong_ui.bash脚本主要是执行如下：

```bash
build_go soong_ui android/soong/cmd/soong_ui //生成soong_ui命令

cd ${TOP}

exec "$(getoutdir)/soong_ui" "$@" //soong_ui入口，执行soong流程

```

比如我们执行make systemimage，到这边就变成了soong_ui –make-mode systemimage

soong_ui代码在build/soong/cmd/soong_ui/main.go 下，主要流程如下：

```bash
func main() {

log := logger.New(os.Stderr)

defer log.Cleanup()

if len(os.Args) < 2 || !inList("--make-mode", os.Args) {

log.Fatalln("The `soong` native UI is not yet available.")

}
```

build.Build(buildCtx, config, build.BuildAll)

主要执行soong/ui/build/build.go，从build.go就可以看到执行soong的大体流程

![哈哈](10408596-6781cb0a7fa6d6bf.jpg)

看一下各个流程

+ Step 1: runMakeProductConfig 主要配置编译参数
+ Step 2: runSoongBootstrap 这个主要是为后面编译工具的使用 ckati soong做环境搭建

runSoong 对工具进行编译

```bash
/.minibootstrap/build.ninja

- Run minibp to generate .bootstrap/build.ninja (Primary stage) - Run minibp to generate .minibootstrap/build.ninja.in

/.bootstrap/build.ninja

- Build any bootstrap_go_binary rules and dependencies -- usually the primary builder and any build or runtime dependencies. - Run the primary builder to generate build.ninja

```

同时还会生成out/soong/build.ninja

+ Step 3: 运行step2生成的ckati，搜集所有的Android.mk文件生成ninja文件，也就是前面提到的 out/build-aosp_arm.ninja
+ Step4: 将out/soong/build.ninja 和out/build-aosp_arm.ninja 合成为combined-aosp_arm.ninja
+ Step5： 运行runNinja 解释combined-aosp_arm.ninja 运行编译过程

## 三 ninja使用

我们可以使用ninja 和生成的combined-aosp_arm.ninja 编译工程或者某一个module.

```bash

$./prebuilts/build-tools/linux-x86/bin/ninja -f out/combined-aosp_arm.ninja

```

同在工程中make一个效果

编译systemimage或者bootimage

```bash

$./prebuilts/build-tools/linux-x86/bin/ninja -f out/combined-aosp_arm.ninja  systemimage
```

上面这样做的目的是，不用重新生成ninja文件，直接进行编译，节省部分时间。这是在没有修改Makefile的前提下
