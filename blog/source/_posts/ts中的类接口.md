---
title: ts中的类接口
date: 2019-07-04 15:27:48
tags: typescript
---

## interface

接口用来对结构中的数据进行检查，描述结构包含属性以及属性的数据类型,编译成的js并不包含此部分的内容。

#### 对象类型的接口

```
interface Test {
  name : string;
  age ?: number;
  [propName:string]: any;
  [propName:number]:any
}
```
propName是额外类型，ts会进行额外的类型检查，如果出现接口中未定义的部分，会报错，此时需要propName，来告诉ts对象可包含的额外特属性。也可以通过类型推断来跳过额外的类型检查

#### 函数类型

```
interface Test {
  // [propName:string]:any
  a: number;
}
function n(a:Test):void {
  console.log(a)
}
n({a:1,b:2} as Test)
```

#### 类类型

类类型接口描述类的实例部分属性，不包括静态部分，constructor是静态部分，需要特殊的处理。

1. 类接口对实例部分进行检测
2. 类构造函数接口对类构造函数部分进行检测
3. createTest函数将两者关联起来，通过此函数创建的对象才会进行全部的检测

```
interface TestClass {
  name: string;
  say(): void;
}
interface TestConstructor {
  new (name:string);
}
function createTest(ctor:TestConstructor,name:string):TestClass {
  return new ctor(name)
}

class Test implements TestClass {
  public name: string;
  constructor (name: string) {
    this.name = name
  }
  say () {
    console.log(this.name)
  }
}

let t = createTest(Test,'age')
```
