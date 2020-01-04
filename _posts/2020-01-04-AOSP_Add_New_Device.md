---
layout: mypost
title: AOSP Add New Device
categories: [AOSP]
---
# AOSP Add New Device

您可以参考本页中的信息为自己的设备和产品创建 Makefile。请注意，与本部分中的其他页面不同，本页中的内容仅适合在创建全新的设备类型时参考，而且仅适合公司编译和产品团队参考。

## 了解编译层

编译层次结构包括与设备的物理结构对应的抽象层。下表中介绍了这些层。每个层都与上一层存在一对多的关系。例如，一个架构可以有多个板，一个板可以有多个产品。您可以将指定层中的某个元素定义为同一层中某个元素的特化元素，从而免去复制操作并简化维护工作。

层|示例|说明
-|-|-
产品|myProduct、myProduct_eu、myProduct_eu_fr、j2、sdk|产品层用于定义所开发产品的功能规范，例如要编译的模块、支持的语言区域，以及针对各语言区域的配置。也就是说，这是总体产品的名称。产品特定变量在产品定义 Makefile 中进行定义。一个产品可以沿用其他产品的定义，这有助于简化维护工作。一种常用的方法是：先创建一个基础产品，其中包含会应用到所有产品的功能，然后再基于这个基础产品创建产品变体。例如，如果有两个产品只是使用的无线技术不同（分别使用 CDMA 和 GSM），那么您可以让这两个产品沿用未定义无线技术的同一个基础产品的定义。
板/设备|sardine、trout、goldfish|设备/主板层代表设备上由可塑材料组成的物理层（即设备的工业设计）。例如，在北美销售的设备可能包括 QWERTY 键盘，而在法国销售的设备则可能包括 AZERTY 键盘。该层还用于展现产品的基本架构图。这些架构图包括板上的外围设备及其配置。所使用的名称只不过是代表不同主板/设备配置的代码。
架构|arm、x86、mips、arm64、x86_64、mips64|架构层用于描述主板上运行的处理器配置和 ABI（应用二进制接口）。

## 使用编译类型

在针对特定产品进行编译时，如果能在最终发布版本的基础上进行细微修改，通常会非常有用。在模块定义中，模块可以通过 **LOCAL_MODULE_TAGS** 指定标记，这些标记可以是以下一个或多个值：optional（默认）、debug、eng。

如果某个模块没有通过 **LOCAL_MODULE_TAGS** 指定标记，则其标记默认为 optional。仅当 **PRODUCT_PACKAGES** 的产品配置需要可选模块时，系统才会安装可选模块。

以下是当前已定义的编译类型：

编译类型|说明
-|-
eng|这是默认的编译类型。<br> + 安装带有 eng 和/或 debug 标记的模块。<br>+ 除了带有标记的模块之外，还会根据产品定义文件安装相应模块。<br >+ ro.secure=0 <br />+ ro.debuggable=1 <br /> + ro.kernel.android.checkjni=1 <br /> + adb 默认处于启用状态。
user|这是旨在用作最终版本配置步骤的编译类型。<br >+ 安装带有 user 标记的模块。<br >+ 除了带有标记的模块之外，还会根据产品定义文件安装相应模块。 <br >+ ro.secure=1 <br >+ ro.debuggable=0 <br >+ adb 默认处于停用状态。
userdebug|除了以下几点之外，其余均与 user 相同： <br> + 还会安装带有 debug 标记的模块。<br> + ro.debuggable=1 <br >+ adb 默认处于启用状态。

## 利用资源叠加层定制版本

Android 编译系统会在编译时利用资源叠加层定制产品。资源叠加层用于指定在默认文件之上应用的资源文件。要使用资源叠加层，请修改项目编译文件，将 **PRODUCT_PACKAGE_OVERLAYS** 设为相对于顶级目录的路径。当编译系统搜索资源时，该路径将成为影子根目录，系统除了在当前根目录中进行搜索外，还会一并在该路径中搜索。

frameworks/base/core/res/res/config.xml 文件中包含用户最常自定义的设置。

要在此文件上设置资源叠加层，请将叠加层目录添加到项目编译文件中，如下所示：

```bash
PRODUCT_PACKAGE_OVERLAYS := device/DEVICE_IMPLEMENTER/DEVICE_NAME/overlay
```

或

```bash
PRODUCT_PACKAGE_OVERLAYS := vendor/VENDOR_NAME/overlay
```

然后，将一个叠加层文件添加到该目录下，例如：

```bash
vendor/foobar/overlay/frameworks/base/core/res/res/config.xml
```

在叠加层 config.xml 文件中找到的所有字符串或字符串数组都将会替换在原始文件中找到的对应字符串或字符串数组。

## 编译产品

您可以通过多种方式组织设备的源文件。我们将以 Nexus 6 为例，简要介绍是如何组织其实施文件的，不过您可以按照自己认为合适的方式组织源文件并进行编译。

为 Nexus 6 实施了一个名为 shamu 的主设备配置。根据此设备配置创建了一个产品以及一个产品定义 Makefile，该 Makefile 用于声明关于设备的产品特定信息，例如名称和型号。您可以查看 device/moto/shamu 目录，了解所有相关配置的具体设置方式。

### 编写 Makefile

以下步骤介绍了如何采用与设置 Nexus 6 产品线类似的方式设置产品 Makefile：

1. 为您的产品创建 device/<company_name>/<device_name> 目录，例如 device/moto/shamu。该目录中将包含您设备的源代码以及编译这些代码所需的 Makefile。

2. 创建一个用于声明设备所需文件和模块的 device.mk Makefile。有关示例，请参阅 device/moto/shamu/device.mk。

3. 创建一个产品定义 Makefile，以便基于设备创建具体产品。以下示例 Makefile 来自于 device/moto/shamu/aosp_shamu.mk。请注意，该产品会通过 Makefile 沿用 device/moto/shamu/device.mk 和 vendor/moto/shamu/device-vendor.mk 文件中的内容，同时还会声明产品特定信息，例如名称、品牌和型号。

```bash
# Inherit from the common Open Source product configuration
$(call inherit-product, $(SRC_TARGET_DIR)/product/aosp_base_telephony.mk)

PRODUCT_NAME := aosp_shamu
PRODUCT_DEVICE := shamu
PRODUCT_BRAND := Android
PRODUCT_MODEL := AOSP on Shamu
PRODUCT_MANUFACTURER := motorola
PRODUCT_RESTRICT_VENDOR_FILES := true

$(call inherit-product, device/moto/shamu/device.mk)
$(call inherit-product-if-exists, vendor/moto/shamu/device-vendor.mk)

PRODUCT_NAME := aosp_shamu

PRODUCT_PACKAGES += \
    Launcher3
```

要查看可添加到 Makefile 的其他产品特定变量，请参阅产品定义变量。

4. 创建一个指向产品的 Makefile 的 AndroidProducts.mk 文件。在此示例中，仅需要产品定义 Makefile。以下示例来自于 device/moto/shamu/AndroidProducts.mk：

```bash
#
# This file should set PRODUCT_MAKEFILES to a list of product makefiles
# to expose to the build system.  LOCAL_DIR will already be set to
# the directory containing this file.
#
# This file may not rely on the value of any variable other than
# LOCAL_DIR; do not use any conditionals, and do not look up the
# value of any variable that isn't set in this file or in a file that
# it includes.
#

PRODUCT_MAKEFILES := \
    $(LOCAL_DIR)/aosp_shamu.mk

```

5. 创建一个包含主板特定配置的 BoardConfig.mk Makefile。有关示例，请参阅 device/moto/shamu/BoardConfig.mk。

6. 创建一个 vendorsetup.sh 文件，以便将您的产品（“午餐套餐”）与编译变体（使用短划线将两者分隔开）一起添加到细分版本中。例如：

```bash
    add_lunch_combo <PRODUCT_NAME>-userdebug
```

这时，您就可以基于同一设备创建更多产品变体了。

### 设置产品定义变量

产品特定变量在产品的 Makefile 中定义。在产品定义文件中维护的变量包括：

参数|说明|示例
-|-|-
PRODUCT_AAPT_CONFIG|创建程序包时使用的aapt配置
PRODUCT_BRAND|对软件进行自定义所针对的品牌（如果有），例如运营商
PRODUCT_CHARACTERISTICS|aapt 特性，用于允许向程序包添加变体特定资源。|tablet、nosdcard
PRODUCT_COPY_FILES|字词列表，例如 source_path:destination_path。在编译相应产品时，应将源路径下的文件复制到目标路径。config/Makefile 中定义了针对复制步骤的规则PRODUCT_DEVICE|工业设计的名称。这也是板名称，编译系统会使用该名称查找 BoardConfig.mk.|tuna
PRODUCT_LOCALES|以空格分隔的列表，用于列出由双字母语言代码和双字母国家/地区代码组成的代码对，以便说明针对用户的一些设置，例如界面语言和时间、日期以及货币格式。PRODUCT_LOCALES 中列出的第一个语言区域会被用作产品的默认语言区域。|en_GB de_DE es_ES fr_CA
PRODUCT_MANUFACTURER|制造商的名称|acme
PRODUCT_MODEL|最终产品的最终用户可见名称
PRODUCT_NAME|总体产品的最终用户可见名称，将显示在“设置”>“关于”屏幕中。
PRODUCT_OTA_PUBLIC_KEYS|产品的无线下载 (OTA) 公钥列表
PRODUCT_PACKAGES|列出要安装的 APK 和模块。|Calendar Contacts
PRODUCT_PACKAGE_OVERLAYS|指明是使用默认资源还是添加任何产品特定叠加层|vendor/acme/overlay
PRODUCT_PROPERTY_OVERRIDES|系统属性分配（采用“key=value”格式）列表

### 设置 ANDROID_VENDOR_KEYS 以通过 USB 进行连接

借助 ANDROID_VENDOR_KEYS 环境变量，设备制造商可以通过 adb 访问正式版。为每个版本生成每台设备都可以接受的密钥并将其存储在内部（如存储在 vendor/oem-name/security/adb/），然后通过 ANDROID_VENDOR_KEYS 让 adb 使用这些规范密钥，而不是随机密钥。

使用 ANDROID_VENDOR_KEYS 环境变量指向生成的加密用 adb 公钥和私钥所在的目录。私钥存储在文件中。公钥存储在 file.pub 中。ANDROID_VENDOR_KEYS 环境变量指向存储生成的密钥对的文件或目录。

该变量被设为一个文件或目录，其中包含使用 adb keygen 文件命令生成的 2048 位 RSA 身份验证密钥对。 这些密钥对是对 ADB 服务器生成的 RSA 密钥对的补充。首次使用 adb 通过 USB 进行连接时，需要使用 RSA 密钥对。

您必须接受主机的 RSA 密钥，才能显式授予 adb 对设备的访问权限。默认情况下，ADB 服务器生成的密钥对以 adbkey（私钥）和 adbkey.pub（公钥）的形式存储在以下密钥存储库目录中：

在 MacOS 上，文件位置可能为：$HOME/.android。在 Windows 和 Linux 上，文件位置为：%USERPOFILE%\.android。在 Windows 上，RSA 身份验证密钥在某些情况下也可能存储在 C:\Windows\System32\config\systemprofile\.android 中。当 ADB 服务器需要密钥时，它会先搜索 ADB 服务器密钥存储库目录。如果找不到任何密钥，它会接着检查 ANDROID_VENDOR_KEYS 环境变量。如果还是找不到任何密钥，本地 ADB 服务器会生成一个新密钥对，并将其保存在 ADB 服务器密钥存储库目录中。