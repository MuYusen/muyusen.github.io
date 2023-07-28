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

这个参数定义了改动被集成到目标时Gerrit采用的合并策略。`通常，提交改动是使用merge的方式的话，那么它所有的依赖也会一起提交进去，只有cherry-pick 的动作，会忽略依赖关系`。 一般不需要修改，使用默认的就可以，不会有错误。但是，如果有与它相关的错误了，就不一般。

`新建项目的默认提交类型`。支持的值为`INHERIT`、`MERGE_IF_NECESSARY`、`FAST_FORWARD_ONLY`、`REBASE_IF_NECESSARY`、`REBASE_ALWAYS`、`MERGE_ALWAYS`和`CHERRY_PICK`。

默认值为`INHERIT`。

submit type 只在项目创建时生效，但是可以在项目创建参数中忽略。如果submit type 没有在创建项目时设置，出于向后兼容的目的，默认值是`MERGE_IF_NECESSARY`而不是`INHERIT`。

配置语法：

```bash
[repository "project/*"]
  defaultSubmitType = MERGE_IF_NECESSARY
[repository "project/plugins/*"]
  defaultSubmitType = CHERRY_PICK
```

Gerrit支持如下几种提交操作：

+ merge if necessary

```bash
这一操作，在submit代码时，Gerrit会尽可能的使用fast-forwards合并到目标分支，否则会自动创建一个merge点。

如果提交的改动的patch-set，刚好在目标分支头，这样尽可能使用fast-forwards方式。

这样的提交动作与 git merge --ff 的操作相同。

通过查看目标分支的历史记录，可以查看所有提交的最初提交时间以及它们最初基于哪个父提交。

```

+ merge always

```bash

这一操作，在submit代码时，不论能不能fast-forwards，Gerrit总是会创建一个merge点。

这样的提交动作与 git merge --no-ff 相同。
```

+ rebase if necessary

```bash
这一操作，在submit代码时，Gerrit会尽可能的使用fast-forwards方式，否则会做自动rebase。

通过这样的提交操作，当执行rebase的时候，已经被评审的的原始提交不会成为目标分支历史的一部分。这意味着关于原始提交的时间以及它们所基于的父提交的信息不会保留在分支历史中。

这种操作制造的线性提交记录，有利于阅读。

不能rebase含有merge点的提交，含有merge点的提交，只能使用 merge if necessary 的方式。

```

+ rebase always

```bash
即使能做fast-forward，Gerrit也会使用rebase的方式。

其他的同 rebase if necessary。
```

+ fast forward only (usage generally not recommended)

+ cherry pick (usage not recommended, use rebase always instead):

此外，提交操作可以设置为`Inherit`，这意味着应用在父项目中配置的值。对于新项目，`Inherit`是默认值，除非默认值被全局defaultSubmitType配置覆盖了。为`All Projects`根项目配置`Inherit`等同于配置`merge if necessary`。

如果未设置submit.action，则默认为“merge if necessary”。
