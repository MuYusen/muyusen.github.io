---
layout: mypost
title: AOSP_Build_Process(P)
categories: [AOSP,BuildSystem]
---
# AOSP_Build_Process(P)

![](p_build_process_accoss.png)

● Make continues to be supported.
● Some new features (VNDK, LTO, Kotlin) are implemented
only in Soong.
● We may remove some rarely-used features after Android P.

```bash
在执行make时，先调用envsetup中的函数make，最终的编译命令是：exec "$(getoutdir)/soong_ui" "$@"

```

## build/envsetup.sh

```bash

function get_make_command()
{
    # If we're in the top of an Android tree, use soong_ui.bash instead ke
    if [ -f build/soong/soong_ui.bash ]; then
        # Always use the real make if -C is passed in
        for arg in "$@"; do
            if [[ $arg == -C* ]]; then
                echo command make
                return
            fi
        done
        echo build/soong/soong_ui.bash --make-mode
    else
        echo command make
    fi
}

function make()
{
    _wrap_build $(get_make_command "$@") "$@"
}
```

## build/soong/soong_ui.bash

```bash
source ${TOP}/build/soong/scripts/microfactory.bash

soong_build_go soong_ui android/soong/cmd/soong_ui

cd ${TOP}
exec "$(getoutdir)/soong_ui" "$@"
```

## build/soong/scripts/microfactory.bash

```bash
# Bootstrap microfactory from source if necessary and use it to build the
# requested binary.
#
# Arguments:
#  $1: name of the requested binary
#  $2: package name
function soong_build_go
{
    BUILDDIR=$(getoutdir) \
      SRCDIR=${TOP} \
      BLUEPRINTDIR=${TOP}/build/blueprint \
      EXTRA_ARGS="-pkg-path android/soong=${TOP}/build/soong" \
      build_go $@
}

source ${TOP}/build/blueprint/microfactory/microfactory.bash
```

## build/blueprint/microfactory/microfactory.bash

```bash
# Set of utility functions to build and run go code with microfactory
#
# Inputs:
#  ${GOROOT}
#  ${BUILDDIR}
#  ${BLUEPRINTDIR}
#  ${SRCDIR}

# Bootstrap microfactory from source if necessary and use it to build the
# requested binary.
#
# Arguments:
#  $1: name of the requested binary
#  $2: package name
#  ${EXTRA_ARGS}: extra arguments to pass to microfactory (-pkg-path, etc)
function build_go
{
}
```