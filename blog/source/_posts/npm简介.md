---
title: npm简介
date: 2017-06-23 15:26:43
tags: npm node.js
categories: npm
---

## get npm 
众所周知，npm是包管理器，但是在平时的使用中，用的最多的是`npm install`/`npm unstall`/`npm start`等等之类的命令，并没有详细的看一下[官网文档](https://www.npmjs.com)。因此在使用中可能会有许多命令只有在需要的时候才会谷歌或者百度一下,闲来无事，看了一眼npm文档，将一些常用的记录下来。

### npm安装

npm 和node.js一起发布，所以当你安装node的时候，会自动在电脑中安装npm。通过命令`node -v`/`npm -v`可以检查所安装的版本。npm和node是分开的项目，并且npm更新频繁。即使刚刚下载安装node，你也可能需要更新npm。更新命令`npm install npm@latest -g`

### npm权限

当你安装包的时候可能会得到EACCES error，这是因为npm没有写入权限。可以通过以下方式解决

1. 更改npm目录的权限
2. 更改npm的默认目录到其他目录
3. 使用包管理器安装node，为你进行管理（mac的brew）

在进行下一步之前，你应该先备份电脑

#### option1:更改npm默认目录的权限
1. 找到npm目录

`npm config get prefix`

在很多系统中，这个目录在`/usr/local`中

2. 将npm目录的所有者改为当前用户的名称(your username)	 

`sudo chown -R $(whoami) $(npm config get prefix)/{lib/node_modules,bin,share}`

这改变了子目录的权限

#### option2：更改npm的默认目录
 
 可以定义npm使用其他的可选目录

 1. 创建目录

 `mkdir ~/.npm-global`

 2. 配置npm使用新目录

 `npm config set prefix '~/.npm-global'

3. 打开或者创建`~/.profile`文件并添加下面一行

`export PATH=~/.npm-global/bin:$PATH`

4. 更新环境变量

` source ~/.profile`

接下来可以使用npm全局安装包进行测试

### option3: 使用包管理器

我的是mac，可以使用Homebrew

### 常用命令

1. `npm install packagename`//	安装在当前目录下的node_modules中
2. `npm init`//创建package.json文件管理本地安装的package,使用`--save`/`--save-dev`将安装package加入到package.json文件的`dependencies`/`devDependencies`中
3. `npm update`//更新
4. `npm uninstall name`和`npm uninstall --save name`//卸载locall安装package并从package.json中删除
5. `npm install packagename -g`//全局安装，
6. `npm update -g name`//全局更新package
7. `npm update -g`//全局更新所以package
8. `npm outdated -g --depth=0`//检查需要更新的package
9. `npm uninstall -g name`//删除
10. npm-run-script
```
npm run-script <command> [--<args>...]
alias: npm run

```
将运行来自package's “scripts"对象的命令。如果没提供命令，将列出所有命令。webpack和vue和angular中都有配置的例子



其它用法详见官方文档
















