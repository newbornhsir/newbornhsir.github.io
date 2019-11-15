---
title: virtualenv的使用.md
date: 2017-11-23 17:30:10
tags: python virtualenv
---

virtualenv用来搭建python的开发环境

## 安装

`pip install virtualenv`

## 创建环境目录

`virtualenv ENV` 


ENV是虚拟开发环境的目录，命令的作用:

1. 创建`ENV/lib/` 和 `ENV/include/` 目录, 目录包含新的开发环境所支持的library. 开发环境下安装的包会放在 `ENV/lib/pythonX.X/site-packages/`目录。
2. `ENV/bin` is created, where executables live - noticeably a new python. Thus running a script with #! /path/to/ENV/bin/python would run that script under this virtualenv’s python.
3. 虚拟环境下安装了`pip` and `setuptools` . This associated pip can be run from ENV/bin/pip.

## 激活环境

`source bin/activate`

激活成功后，命令行提示符前出现目录名称

## 取消激活

`deactivate`