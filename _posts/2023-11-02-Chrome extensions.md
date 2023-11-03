---
title: 写一个简单的Chrome扩展
tags: 技术
---

<!--more-->

我一直在浏览器中开过多页面和打开浏览器保持上次浏览页面的习惯，并且喜欢把过多的页面按照日期保存到书签中再关闭，但这样操作步骤非常麻烦：按下Ctrl+Shift+D，选择文件夹，更改文件夹名为日期，点击确定，依次关闭所有标签栏。

查找了一些插件都不太想用，因为很多都没有存入书签中。我觉得保存到书签中是最安全最不容易丢失的方案，所以直接自己写一个简单的好了。

# 导入配置

开发基本步骤：[创建和发布自定义 Chrome 应用和扩展程序](https://support.google.com/chrome/a/answer/2714278?hl=zh-Hans)

首先按照Google文档导入一个最简单的页面：https://developer.chrome.com/docs/extensions/mv3/getstarted/development-basics

# 基本功能

首先不考虑扩展性，把基本功能先写完，思考一下要用到什么API，要读取并关闭所有标签栏，并且把信息存入书签，所以需要用到tabs和bookmarks的api，查找后发现v3版本支持promise写法，继续稍微阅读一下文档并且写一写代码，这样基本流程就可以跑通了。

# 调整功能



# 页面美化

 
