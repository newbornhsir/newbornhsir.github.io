---
title: hexo+github搭建个人博客
date: 2017-05-09 11:42:55
tags: hexo+github
---
## 搭建个人博客
因为自己电脑需要更换，但目前没有资金，想创建博客也希望在更换电脑的时候很好的迁移，网上查了一下资料，发现这么一种方法。自己试了一下目前是成功的，为了防止下次更换电脑时忘记具体流程，所以在操作完成之后凭借印象写下这篇博客。具体操作中可能会有和当时操作不一样地方，具体没有检查，或许会有错误
### 安装node.js和git
- 具体操作和环境配置看网上配置教程
### 在github上创建仓库
- 仓库名称为username.github.io,网上说仓库必须以这个命名规范，其他的命令是否可以不清楚，没有测试
- 仓库中新建分支hexo存放博客源文件，master分支存放发布的文件
- 仓库克隆到本地
### 安装hexo
- 执行 npm init (这一步我添加的，具体操作没试过，可以不用这一步)
- npm install hexo-cli --save
- hexo init blogName
- cd blogName
- npm install
- 执行hexo server，打开端口查看效果
### hexo 远程git仓库配置
- 打开blogName目录下的_config.yml文件
- 修改deploy

	```
	deploy:
	  type: git
	  repo: git@github.com:username/username.github.io.git
	  branch: master
	```
- 其它配置按照自己喜好修改
- 安装 npm install hexo-deployer-git --save
### 测试
- 执行 git push 提交命令到hexo分支，在远程仓库的hexo分支查看文件是否上传
- 执行hexo d -g命令，提交完成，打开`https://username.github.io`查看博客
### 主题配置
- 网上搜索hexo主题，挑选自己喜欢的下载放到thems文件夹下
- 修改blogName下_config.yml文件中的theme为所应用主题的名称
- 主题的其它修改在主题文件目录下的_config.yml文件中