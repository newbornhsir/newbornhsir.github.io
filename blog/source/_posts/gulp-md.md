---
title: gulp.md
date: 2018-03-19 11:54:57
tags: gulp
---

### gulp

简介



gulp自动化构建，配合一些插件使用，可以完成如sass预处理，js 压缩，html模版，动态刷新等工作。gulp是基于node中的stream，在stream上做了一次封装成vinyl file。通过pipe(管道)，传递流进行传递数据。

使用流的好处是存储在内存中，减少I/O,因此效率更高.

## 这里是还未具体了解的知识点

- stream

stream 是nodejs的一个模块

gulp的流式处理： 上一个的输出，是下一个的输入

- pipe

- vinyl file

### gulp vs grunt

- gulp 易于书写和阅读
- 减少频繁的I/O，快速构建
- 易于使用和学习
- task依赖orchestrator，支持最大可能并发

### gulp vs webpack


### api

##### src(globs[,options])

匹配globs中的路径文件，返回一个vinyl files的stream，可以pipe到别的插件。 option为可选的一些配置

##### dest(path[,options])

输出到路径， path为string或者函数 ? 函数怎么使用

##### task(name[,deps],fn)

创建一个任务，可选的deps数组，是其它的依赖任务，会在当前任务运行前执行

##### watch(glob[,opts],tasks)

监视文件的变动

glob: string | Array

opts: 传递给gaze的参数。 gaze是什么？

tasks: array，文件变动后需要执行的任务

##### start(taskName)

执行一个任务,这里的参数？



### 基本用法
- 安装gulp

`npm install gulp`

- 创建配置文件

1- 在项目根目录创建一个gulpfile.js的文件，运行gulp的时候会执行这个文件

2- 多个配置文件，可以使用 `gulp --gulpfile <filename>`,来指定路径

3- gulpfile.js中创建任务

```

// demo,当在命令行中执行gulp的时候，默认执行配置文件的default任务
// 也可以 gulp <taskName> 来执行指定的任务
/* gulp 只有你需要熟知的参数标记，其他所有的参数标记只在一些任务需要的时候使用。

 *   -v 或 --version 会显示全局和项目本地所安装的 gulp 版本号
 *   --require <module path> 将会在执行之前 reqiure   
 *   一个模块。这对于一些语言编译器或者需要其他应用的情况来说来说很有用。你可以使用多个--require
 *   --gulpfile <gulpfile path> 手动指定一个 gulpfile 的路径，这在你有很多个 gulpfile 的时候很有用。这也会将 CWD 设置到该 gulpfile 所在目录
 *   --cwd <dir path> 手动指定 CWD。定义 gulpfile 查找的位置，此外，所有的相应的依赖（require）会从这里开始计算相对路径
 *   -T 或 --tasks 会显示所指定 gulpfile 的 task 依赖树
 *  --tasks-simple 会以纯文本的方式显示所载入的 gulpfile 中的 task 列表
 *  --color 强制 gulp 和 gulp 插件显示颜色，即便没有颜色支持
 *  --no-color 强制不显示颜色，即便检测到有颜色支持
 *  --silent 禁止所有的 gulp 日志
 *  gulp <task> <otherTask> 执行任务
 */
 var gulp = require("gulp");
gulp.task(taskName,function(){

})

```


### 常用的插件

#### browserify

- gulp-load-plugins

- browserify



