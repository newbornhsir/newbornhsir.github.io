---
title: webpack01.md
date: 2019-04-03 19:20:29
tags: webpack
---

## 简介

webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。
当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，
其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。
webpack四个核心概念
- entry
- output
- loader
- plugins

### entry
入口起点，决定从哪个模块开始构建依赖关系。可以配置多个入口起点

### output
指定输出打包文件的配置，如路径，文件名等

### loader
loader可以让webpack加载非javascript文件。webpack只识别javascript，loader可以加载其它模块让webpack来处理。
loader有两个重要的配置:
- test: 标示出加载的文件
- use: 指明使用的loader类型

### plugins
用来处理特定的任务，如对打包进行优化和压缩等，相当于扩展