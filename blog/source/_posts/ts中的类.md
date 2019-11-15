---
title: ts中的类
date: 2019-07-04 11:44:47
tags: typescript
---
### typescript中的class

#### 静态属性
存储在类本身的属性或者方法，与实例无关，只能通过ClassName.attr访问，不能通过实例对象访问。

静态属性
```
class Test {
  static nameAttr = 19
  static say () {
    console.log('i am static method')
  }
}

```

编译后的结果， 类似定义在构造函数对象自身的属性，而不是定义在构造函数原型，因此只对构造函数有影响，并不会影响实例对象。
```
var Test = /** @class */ (function () {
    function Test() {
    }
    Test.say = function () {
        console.log('i am static method');
    };
    Test.nameAttr = 19;
    return Test;
}());
```

#### 实例属性

和实例相关的属性，可以被继承,不使用static修饰符
```
// ts
class Test {
  static nameAttr = 19
  selfName = 'self'
}
```
```
// 编译之后的js
var Test = /** @class */ (function () {
    function Test() {
        this.selfName = 'self';
    }
    Test.nameAttr = 19;
    return Test;
}());
```

##### 构造函数
构造函数是创建对象时候需要动态赋值的一些属性，也是实例属性
```
// ts
class Test {
  static nameAttr = 19
  selfName = 'self'
  testname : string
  constructor (name) {
    this.testname = name
  }
}
```
```
// 编译结果
var Test = /** @class */ (function () {
    function Test(name) {
        this.selfName = 'self';
        this.testname = name;
    }
    Test.nameAttr = 19;
    return Test;
}());
```

#### public/private/protect

#### public
公共的实例属性，默认属性都是public的形式，可以被继承和访问

#### private

私有属性，只有在类的内部才能访问，但是编译成js后，和public类型的属性一致， 但是如果在ts中，在类外部访问就会报错。
```
// ts
class Test {
  static nameAttr = 19
  selfName = 'self'
  testname : string
  constructor (name) {
    this.testname = name
  }
  private publicAttr = 13
}

new Test('haha').publicAttr // 这里会报错
```
```
// 编译后的结果， 并没有什么不同
var Test = /** @class */ (function () {
    function Test(name) {
        this.selfName = 'self';
        this.publicAttr = 13;
        this.testname = name;
    }
    Test.nameAttr = 19;
    return Test;
}());
```

#### protected
protected和private类似， 但是protected属性可以被继承,因此可以在子类的内部访问

#### 继承

ts使用extends实现继承
```
class Test {
  static nameAttr = 19
  selfName = 'self'
  testname : string
  constructor (name) {
    this.testname = name
  }
  private privateAttr = 13
  protected protectedAttr='122'
}

class Child extends Test {
  name: string = 'child'
}
```

```
var __extends = (this && this.__extends) || (function () {
    var extendStatics = function (d, b) {
        extendStatics = Object.setPrototypeOf ||
            ({ __proto__: [] } instanceof Array && function (d, b) { d.__proto__ = b; }) ||
            function (d, b) { for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p]; };
        return extendStatics(d, b);
    };
    return function (d, b) {
        extendStatics(d, b);
        function __() { this.constructor = d; }
        d.prototype = b === null ? Object.create(b) : (__.prototype = b.prototype, new __());
    };
})();
var Test = /** @class */ (function () {
    function Test(name) {
        this.selfName = 'self';
        this.privateAttr = 13;
        this.protectedAttr = '122';
        this.testname = name;
    }
    Test.nameAttr = 19;
    return Test;
}());
var Child = /** @class */ (function (_super) {
    __extends(Child, _super);
    function Child() {
        var _this = _super !== null && _super.apply(this, arguments) || this;
        _this.name = 'child';
        return _this;
    }
    return Child;
}(Test));

```

js通过借用继承来实现的继承，对于子类需要constructor，需要手动执行super，来继承父类职工构造函数的部分

##### 抽象类
抽象类作为基类，一般不会被实例化，包含成员的实现细节。abstract定义抽象类和抽象类中定义的方法。抽象类的方法不包含实现细节，但必须在子类中实现。

#### 总结

类分为实例属性和静态属性，静态属性通过类名访问，实例属性通过实例访问。继承通过extends实现，只可以继承public或者protected的属性，private和protected属性只能在类内部访问。当子类也存在constructor函数时，需要手动执行super继承父类构造函数初始化。
