---
title: 在国内修改npm源的正确姿势
date: 2019-06-23 
tags: npm
categories:
---

国内的淘宝前端团队做了npm的国内镜像并且制作了新的 cnpm 工具

但是 cnpm 有个问题，用它下载安装的模块都是以软链形式存在的，本来模块文件就多，再加个软链又多一倍文件，导致有些编辑器（sublime text）和 IDE（WebStorm）检索目录时非常慢。 

而且cnpm 有时候会出现一些稀奇古怪的依赖问题

使用 `npm i package --registry=https://registry.npm.taobao.org` 这个方式是从指定仓库下载依赖但是因为 npm 的本身原因，下载速度还是不快。

一、添加 .npmrc

.npmrc 是 npm runtime config 的意思，可以通过这个文件在命令行环境中为 npm 提供环境变量。 `.npmrc`

npm 默认的源为 https://registry.npmjs.org/，我们可以通过这个配置修改 npmrc 文件来修改项目默认的 registry 地址：

```
# .npmrc
registry = 'https://registry.npm.taobao.org'  
```

1. $ npm config set registry https://registry.npm.taobao.org 全局修改 npm 源；
2. nrm - NPM registry manager；
3. npminstall - Make npm install fast and easy.






