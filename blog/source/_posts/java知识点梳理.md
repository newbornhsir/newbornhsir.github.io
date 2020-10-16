---
title: java知识点梳理
date: 2020-04-13 10:45:22
tags: java
category: java
---
java知识点梳理

## 数据类型

数据类型分为基本类型和引用类型,基本数据类型和引用类型的区别主要在于基本数据类型是分配在栈上的，而引用类型是分配在堆上的。

> 堆栈概念
？。。。。

### 基本类型

- byte
- short
- int
- long
- chart
- float
- double
- boolean

### 引用类型

除去基本类型，剩下的都是引用类型，引用类型存储在堆中。（堆栈区别）

#### String

##### 字符和字符串

字符是基本类型，使用`''`表示，字符串是引用类型，使用`""`表示，可存储任意长度字符。

#### 数组

```java
int[] arr = new int[5];// 未初始化
// int[] ns = new int[] { 68, 79, 91, 85, 62 }; // 创建时候直接初始化
// int[] ns = { 68, 79, 91, 85, 62 }; // 简写
System.out.println(arr);
int len = 0;
for(; len < arr.length; len++) {
    System.out.println(arr[len]);
}
```

- 创建数组使用new关键字，初始化时机可选
- 未初始化，系统会赋值默认值
- 数组访问通过索引，超出数组长度会报错

## 变量

### 创建变量

类型名 变量名 【 = 值】,也可以先命名后初始化

```java
String a;
a = "s";
```

## 流程控制

### 逻辑运算符

`>` `<` `==` `&` `|` ....

### 流程控制语句

#### 条件语句

- if
- switch

#### 循环语句

- while
- for

```java
public class HelloWorld {
    public static void main(String[] args) {
        int i = 10;
        // 循环
        // while(i > 0) {
        //     System.out.println(i);
        //     i -= 1;
        // }

        // for(; i > 0; i--) {
        //     System.out.println(i);
        // }

        for(int b = 3; b > 0; b--) {
            System.out.println(b);
        }

        // 条件

        if (i > 10) {
            System.out.println(i);
        } else {
            System.out.println("x");
        }

        switch (i) {
            case 10:
                System.out.println(i);
                break;
            default:
                System.out.println("x");
                break;
        }
    }
}
```

### break和continue

## 类

TODO:
类： 构造对象的蓝图或模版
实例：类创建出来的一个实例对象，类可以创建多个实例

定义类使用class关键字，创建实例使用new关键字

类关系： 依赖(use-a)、聚合(has-a)、继承（is-a）

```java
public class Relationship {
    private class Order {
        /**
         * 订单和商品是聚合关系， has-a, 订单需要包含商品信息
         */
        private List<Product> productList;
        public List<Product> getProductList() {
            return productList;
        }
        public void setProductList(List<Product> productList) {
            this.productList = productList;
        }
        /**
         * 订单和账户是依赖关系 use-a
         */
        public void pay(Account account) {
            boolean hasMoney = account.hasMoney();
        }
    }
    private class Account {
        public boolean hasMoney() {
            return true;
        }
    }
    private class Product {}
}
```

### 构造器

通过构造器`constructor`创建对象并初始化状态，构造器和类同名，在创建实例的时候，构造器会运行，以便将实例域初始化为所希望的状态。

1. 构造器伴随new调用自动执行，不能对一个已存在的对象调用构造器来初始化状态
2. 构造器和类同名
3. 构造器没有返回值
4. 构造器可以存在多个，通过函数重载和函数签名自动判断实例化时候调用,实现不同形式的初始化
5. 用户不提供构造器，会默认添加一个无参数的构造器
6. 构造器中可以通过this来调用其它构造器
7. super可以调用父类的构造器
8. 继承关系中, 父类不存在默认的构造器，子类必须在构造器中使用super显示的调用父类的构造器

创建实例的构成：

1. 分析继承关系 child -> parent;
2. 调用构造器 parent -> child;
3. 因此子类super调用父类构造器必须放在语句块的顶部

### 实例域和静态域

域翻译成英文是filed， 也就是字段或者属性

#### 实例域

类是创建对象的蓝图或者模版，由类创建的实例都有各自的实例域，private,protected定义的属性是实例域，实例域使用final修饰，则只能在初始化的时候修改状态或者在定义实例域的时候初始化， 二选一，实例创建完成后不允许修改

#### 静态域

类所特有的属性，且被所有的实例共享。通过static关键字定义

### 静态方法和实例方法

静态方法属于类所有，只能访问静态域， 实例方法属于实例所有，可以访问实例域和静态域。实例方法会传入个隐式参数：当前实例对象，可以通过this关键字获取引用

### 初始化块

类中花括号包含的部分，在执行构造方法前执行，可执行初始化操作，不常用，代码块前加上static是静态初始化

### 访问限制

1. private: 本类可以访问
2. protected: 本包和子类可以访问
3. public: 公开

### 包

包的形式组织代码，包映射出目录结构，classpath: 定义包查找的路径

### 继承

1. java只支持单继承
2. 子类无法直接访问父类的私有域，需要通过super参数来调用
3. 重写
4. 阻止继承final类和方法

#### 方法调用

1. 检查声明类型和方法名，获取可用的方法，当前类的方法和父类的public方法
2. 根据函数签名重载解析
3. 静态绑定直接调用
4. 动态绑定则调用对象实际类型中最合适的方法

#### 转型

1. 只能在类的继承层次结构转
2. 向下转型前先用instanceof判断
3. 向上转型比向下转型安全

### 抽象类

基类使用做继承，派生其它类的基类，不需要实例化，则可以设计成抽象类

### 多态

基类类型对象可以是基类，也可以是其它子类对象，子类对象重写实现多态TODO:

```java
public class Demo03 {
    private static abstract class Parent {
        abstract void say();
    }
    private static class ChildFirst extends Parent {

        @Override
        void say() {
            System.out.println("i am child first");
        }
    }

    private static class ChildSecond extends Parent {

        @Override
        void say() {
            System.out.println("i am child second");
        }
    }

    public static void main(String[] args) {
        Parent child1 = new ChildFirst();
        Parent child2 = new ChildSecond();
        child1.say();
        child2.say();
    }
}
```

### 包装器和自动装箱

```java
ArrayList<Integer> l = new ArrayList<>();
l.add(3) === l.add(Integer.valueOf(3))
```

### 枚举类

TODO:

```java
public enum EnumClass {
    OK(0, "成功");

    private int code;
    private String message;
    EnumClass(int code, String message) {
        this.code = code;
        this.message = message;
    }

    public int getCode() {
        return code;
    }

    public String getMessage() {
        return message;
    }

    public static void main(String[] args) {
        System.out.println(EnumClass.OK.ordinal());
        System.out.println(EnumClass.OK);
    }
}
```

### 集合

集合和map

### 反射

能够分析类能力的程序称为反射。java在运行期间为所有对象维护运行类型标识，这个信息跟踪着每个对象所属的类，保存这些信息的类是Class。

分析类： constructor, field, method

### 接口

接口中不能包含实例域和静态方法， 所有方法都是public, 常量 public static final, java se8可以添加静态方法

java是但继承，但是可以实现多个接口

### 函数式编程

lambda表达式，函数式接口：只有一个方法的接口

### 内部类

### 代理

Proxy

### 范型

范型类
范型方法
范型类型限定
类型擦除： 编译后擦除范型信息为原始类型，同时会对数据强制类型转换

不能用基本类袭实例化类型参数
运行时类型查询只适用于原始类型 a instanceof Pair<String>;

### 作用域

### 模块

### 核心类

### classpath和jar

维护命名空间

## 异常

## java异常

## 异常捕获

## 自定义异常

## 断言

## 日志

## 注解

## IO

## 正则

## 时间和日期

## 网络编程

## 数据库操作

## 线程和进程

## 单元测试

## gc

## javaweb

tomcat 创建javaweb项目打包成war包可以运行在服务器上，供用户访问
servlet 动态web资源, web.xml配置映射信息。
实现servlet，三种方式
servlet-map配置， xml配置， 注解配置
HttpServletRequest, HttpServletResponse
jsp在不使用ajax情况下的动态网页, 
