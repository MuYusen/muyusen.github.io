---
layout: mypost
title: Gerrit的配置参数：defaultSubmitType
categories: [Gerrit]
---

# Gerrit的配置参数：defaultSubmitType

**repository.\<name\>.defaultSubmitType**

写在前面：

众所周知，Gerrit推送代码有两种方式：一种，走代码评审 **git push origin HEAD:refs/for/targetbranch**；另一种是直接入库，**git push origin HEAD：refs/heads/targetbranch**。

defaultSubmitType配置控制的是代码评审结束（+1、+2、点击了“submit”按钮）之后，Gerrit的动作。也就是代码从暂存区（refs/for/targetbranch）合到目标分支的动作。

这个参数一般不需要修改，使用默认的就可以，不会有错误。但是，如果有与它相关的错误了，就不一般。

`新建项目的默认提交类型`。支持的值为`INHERIT`、`MERGE_IF_NECESSARY`、`FAST_FORWARD_ONLY`、`REBASE_IF_NECESSARY`、`REBASE_ALWAYS`、`MERGE_ALWAYS`和`CHERRY_PICK`。

默认值为`INHERIT`。

submit type 只在项目创建时生效，但是可以在项目创建参数中忽略。如果submit type 没有在创建项目时设置，出于向后兼容的目的，默认值是`MERGE_IF_NECESSARY`而不是`INHERIT`。
