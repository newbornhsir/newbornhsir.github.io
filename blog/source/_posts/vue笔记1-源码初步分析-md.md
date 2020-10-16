---
title: vue笔记1-源码初步分析.md
date: 2018-03-08 21:58:51
tags: vue
categories: vue
---

## Vue 的初始化过程

```flow
st=>start: merge option
op1=>operation: initLifecycle
op2=>operation: initEvents
op3=>operation: initRender
op4=>operation: callHook
op5=>operation: initInjections
op6=>operation: initState
op7=>operation: initProvide
op8=>operation: callHook
e=>end: mount

st(right)->op1->op2(right)->op3->op4(right)->op5->op6(right)->op7->op8(right)->e
```

### 1. 合并options

判断是否为组件， 采用不同的合并方式

#### 1.1 非组件options合并

1. 解析Vue的options和Vue原型的option, 递归解析，合并成一个option， 期间判断是否和缓存相同，决定是否需要重新合并
2. 将解析出的Vue option和传入的option合并成一个对象
