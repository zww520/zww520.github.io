---
title: Ubuntu部署hexo
date: 2026-06-28 00:17:29
categories:
    - Hexo
tags: 
    - Hexo
---

## Ubuntu部署hexo

Ubuntu部署hexo

<!-- more -->

### hexo介绍

Hexo 是一个快速、简洁且高效的静态博客框架。它基于 Node.js 运行，能将你用 Markdown 等标记语言写好的文章，在几秒内渲染成带有精美主题的静态网页

官方说明文档链接：[快速、简洁且高效的博客框架](https://hexo.io/docs/)

### 部署步骤

**1. 安装 Node.js **

官网教程：[Download Node.js](https://nodejs.org/en/download#debian-and-ubuntu-based-linux-distributions)

```powershell
# Download and install nvm:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.5/install.sh | bash
# in lieu of restarting the shell
\. "$HOME/.nvm/nvm.sh"
# Download and install Node.js:
nvm install 24
# Verify the Node.js version:
node -v # Should print "v24.18.0".
# Verify npm version:
npm -v # Should print "11.16.0".xxxxxxxxxx # Download and install nvm:curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.5/install.sh | bash# in lieu of restarting the shell\. "$HOME/.nvm/nvm.sh"# Download and install Node.js:nvm install 24# Verify the Node.js version:node -v # Should print "v24.18.0".# Verify npm version:npm -v # Should print "11.16.0".wsl --install
```

**2. 安装Hexo**

官网教程：[Download Node.js](https://nodejs.org/en/download#debian-and-ubuntu-based-linux-distributions)

```shell
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

**3. 常用命令**

清除 Hexo 生成的静态文件和缓存

```shell
npx hexo clean
```

将 Markdown 格式的源文件生成完整的静态网页文件

```shell
npx hexo clean
```

将生成的静态网站文件部署到远程服务器或托管平台

```shell
npx hexo d
```

在本地启动一个开发服务器，可以在浏览器中实时预览博客效果

```shell
npx hexo s
```

在 `source/_posts` 下新建博客页面，

```shell
npx hexo new post "文章标题"
```







