---
layout: mypost
title: 更换GitHub Pages博客主题
categories: [GitHub]
---

## 选择主题

+ 从[jekyllthemes](https://jekyllthemes.io/github-pages-themes)中选择喜欢的主题

+ 免费的主题通常都是GitHub的开源项目，访问GitHub项目主页

## 以主题为模板创建自己的Repository

+ 通过点击[Use this template]创建自己的Repository
+ 以[yourgithubname].github.io命名新建的Repository

## 修改通用配置(_config.yml)

+ 修改根目录（==根目录不正确的话会导致样式或图片加载失败==）

```bash
baseurl: "/"
```

+ 修改博客名称

```bash
name: yourblogname
```

+ 修改博客描述

```bash
description: yourblogdesc
```

## 常用目录介绍

+ 页面模板目录

```bash
_pages包含如下文件
about.md        自我介绍页
categories.md   分类标签页
search.md       搜索页
```

+ 发布的博客目录

```bash
_posts
yyyy-MM-dd-title.md 博客文件名称样式
```

## 访问博客

```bash
https://yourgithubname.github.io
```

## Demo

我选择的主题

[Reverie](https://jekyllthemes.io/theme/reverie)
