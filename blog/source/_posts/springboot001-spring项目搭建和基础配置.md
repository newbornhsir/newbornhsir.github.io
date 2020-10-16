---
title: spring
date: 2020-04-15 09:51:43
tags: spring springBoot java
category: java spring springBoot
---

## spring简介

spring是模块化，其核心是spring-core，spring-bean,spring-context,spel,

spring-cores是基本部分，包括ioc和di,

spring-beans提供beanFactory

context模块

spel

### 创建

读取配置文件创建ApplicationContext，应用上下文

1. 三种读取配置文件的方式：ClassPathXmlApplicationContext、FileSystemXmlApplicationContext、WebXmlApplicationContext
2. 读取配置的bean信息，加载bean以及实例化bean的时机

#### 配置bean

- class: 必须的属性，指明创建bean的类
- id/name: 可以通过id/name来获取标识bean
- scope: bean的作用域，支持5中： singleton: 只会创建一个共享的备案, prototype：每次调用都会创建新的, request, session, global-session： 后三种仅适用webApplicationContext环境
- constructor-arg: 创建bean的构造函数参数
- properties: bean的属性，会通过set方式设置
- auto-wire: 自动注入？ 默认no 可以是no, byName, byType, constructor
- lazy-init: 是第一次请求还是创建Application的时候实例化, lazy-init读取配置文件的时候不会加载类，而是在调用的时候加载并实例化
- init-method: 所有属性设置后调用，xml配置，也可以通过implement InitalizingBean的afterPropertiesSet来实现
- destroy: 包含bean容器销毁时候调用，可以xml或者实现DisposableBean： destory方法

#### 后置处理器自动调用，需要实现BeanPostProcessor接口， Ordered接口定义实现顺序

#### IOC

配置bean实现IOC

#### DI

配置bean实现DI

- property DI

基于setter方式，通过property指定 name是属性名称， ref另一个bean的引用， value指定值

- constructor DI

constructor-arg ref指定传递注入参数的实例，可以通过type来指定参数的类型，否则需要和构造函数形式参数的顺序一致，也可以用index显示的指明参数的索引

- 注入内部bean

java中的类或者内部bean

- 注入集合

- 自动装配（DI）

xml配置auto-wire属性

- 基于注解的自动注入

1. @Required: 作用在setter方法上，表明这个属性必须在xml中做配置，否则就会抛出异常, 5.1之后废弃
2. @AutoWired：作用到字段、方法或者构造方法中，会在ApplicationContext寻找到符合类型的bean注入
3. @Qualifier：多个相同类型的bean的时候指定被注入的是哪一个bean

### 基于java的配

- @Configuration
- @Bean

### 事件处理和自定义事件

### AOP

### 引入spring-context

目前问题是servlet请求创建的实例不是ApplicationContext中的bean，通过使用ApplicationContext来使用bean比较麻烦

## spring-mvc

mvc的开发模式，通过DispatcherServlet来进行调度处理请求，DispatchServlet也是一个servlet，需要进行配置，可以通过xml或者java来进行配置

DispatcherServlet需要一个扩展ApplicationContext的WebApplicationContext, 和当前Servlet的ServletContext建立关联，一个应用可以多个WebApplicationContext，即多个DispatcherServlet

DispatcherServlet 通过WebApplicationContext中特殊的bean来实现MVC, 用户也可以自定义这些行为，不定义采用默认的方式

example:

- 处理映射关系的bean: RequestMappingHandlerMapping 或者SimpleUrlHandlerMapping
- HandlerAdapter: 调用请求的handler而不关心具体的调用细节
- 异常处理
- 视图解析
- 主体解析
- ....

mvc使用DispatcherServlet来调度请求，查找合适的控制器并调用特定的请求方式，返回数据，经过视图解析进行渲染

1. 配置servlet来进行代理请求
2. 根据servlet-name创建【servlet-name】-servlet.xml文件来进行WebApplicationContext配置
3. WebApplicationContext继承ApplicationContext，不过多了web开发时候的一些特性

- 配置DispatcherServlet
- controller, 可以做接口或者给视图Modal添加数据通过视图解析在jsp上渲染

## 项目创建

### maven手动创建

- maven创建项目
- pom.xml中添加spring的depency

### 使用spring initializr

### 尝试

#### 创建一个restfull接口

#### 数据库连接

##### 目录结构

- dao: 数据模型
- controller：控制器
- service： 服务
- repository: mapper
- util
  
调用流程

```flow
st=>start: 调用controller
op1=>operation: controller调用service调用mapper 调用 找到xml 调用 数据库 调用 dao进行转换
ed=>end: 结束

st->op1->end
```

##### 实现文章展示和分页功能

- application.yml配置数据库信息
- MappperScan扫描映射关系
- xml文件处理sql操作
- baomidou
- pagehelper

## spring介绍

### IOC

#### 管理Bean

spring是创建Bean的容器，替代手动创建的过程。通过依赖注入，来创建Bean和使用Bean。配置有三种方式：xml、注解、Java配置。

##### xml配置

##### 注解配置

##### java配置

#### Bean

##### scope

##### 初始化方法

##### 表达式

##### 环境

##### 事件，bean与bean之间通讯

### AOP
