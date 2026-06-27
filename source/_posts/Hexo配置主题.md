---
title: Hexo配置主题
date: 2026-06-28 00:31:40
categories:
    - Hexo
tags: 
    - Hexo
---

## Hexo配置主题

Hexo配置 **hexo-theme-fluid** 主题

<!-- more -->

### hexo-theme-fluid 主题介绍

Fluid 是基于 Hexo 的一款 Material Design 风格的主题，由 [Fluid-dev](https://github.com/fluid-dev) 负责开发与维护

主题 GitHub: [https://github.com/fluid-dev/hexo-theme-fluid(opens new window)](https://github.com/fluid-dev/hexo-theme-fluid)

预览网站：[Fluid's blog](https://fluid.ist/)

### 配置步骤

**1. 下载 hexo-theme-fluid **

```powershell
git clone https://github.com/fluid-dev/hexo-theme-fluid.git
```

**2. 创建主题目录**

在博客 `themes` 目录下新建文件夹 `fluid` ，然后将主题文件复制到改目录

**3. 修改 hexo 配置**

在博客目录下创建 `_config.fluid.yml`，将主题的 [_config.yml ](https://github.com/fluid-dev/hexo-theme-fluid/blob/master/_config.yml)内容复制过去

如下修改 Hexo 博客目录中的 `_config.yml`

```
theme: fluid  # 指定主题

language: zh-CN  # 指定语言，会影响主题显示的语言，按需修改
```





