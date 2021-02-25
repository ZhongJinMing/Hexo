---
title: webpack学习
date: 2019-08-13 15:49:05
tags: webpack
category: webpack
---

## 安装

1. 要安装最新版本或特定版本，请运行以下命令之一：

   ```bash
   npm install --save-dev webpack
   npm install --save-dev webpack@<version>
   ```

   如果你使用 webpack 4+ 版本，你还需要安装 CLI。

   ```bash
   npm install --save-dev webpack-cli
   ```





`css-loader`以字符串形式读取CSS文件。你可以用`raw-loader`在很多情况下都会产生同样的效果。因为它只读取文件内容而没有其他内容，所以它基本上是无用的，除非你用另一个加载程序将它链接起来。

`style-loader`获取这些样式并创建`<style>`页中的标记`<head>`包含这些样式的元素。

如果你看一下里面的javascript bundle.js使用后`style-loader`你将在生成的代码中看到一个注释：

> //Style-Loader：通过添加标记向DOM添加一些CSS