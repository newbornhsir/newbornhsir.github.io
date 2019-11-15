---
title: webpack配置
date: 2019-08-14 18:04:15
tags: webpack
---



### es6转es5

es6支持需要使用[babel](https://babel.docschina.org/setup#installation)

#### 配置loader
```
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader'
        },
        // 忽律 node_modules目录
        exclude: /node_modules/
      }
    ]
  }
```
#### 安装@babel/preset-env进行语法转换,配置.babelrc

`npm install @babel/preset-env --save-dev`

安装`@babel/plugin-transform-runtime`，使支持新的es特性

项目目录下创建.babelrc文件
```
{
  "presets": ["@babel/preset-env"],
  "plugins": ["@babel/plugin-transform-runtime"]
}
```

### js校验, eslint配置

#### 安装eslint, eslint-loader

`npm i eslint eslint-loader --save-dev`

#### 配置loader

```
  rules: [
      {
        test: /\.js$/,
        use: ['babel-loader', 'eslint-loader'],
        // 忽律 node_modules目录
        exclude: /node_modules/
      }
    ]
```
#### 创建eslint配置文件.eslintrc.json

### 每次打包清除旧的打包内容



### 提取公共代码 ???

多入口时候，引用同一个文件, 不配置的时候会被打包到每个引用文件中去，加大了代码体积。
new webpack.optimize.CommonsChunkPlugin(options)
wepack4x使用optimization.splitChunks 

<font color=red>配置之后，公用代码块可以，但是第三方库jquery分离不报错，但是不能使用。</font>

```
  optimization: {
    splitChunks: {//  分割代码
      cacheGroups: {// 缓存组
        common: {
          // 表明哪些chunks需要优化，可以传递字符串或者函数
          // 合法的字符串: all , async, initial
          // 函数可以更灵活控制需要优化的chunks
          chunks: 'all',
          // 最少被包含的数量
          minChunks: 2
        },
        vendor: {
          test: /node_modules/,
          chunks: 'initial',
          minChunks: 2,
          priority: 1,
          enforce:true
        }
      }
    }
  },
```



