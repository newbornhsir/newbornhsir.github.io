---
title: es6特性
date: 2019-11-14 21:04:51
tags:
---
## 变量声明

es6提供了新的变量声明方式：`let` `const`。和`var`声明相比，有以下特点：

1. 不存在变量提升

    ```js
      // es5
      function test(condition){
        if(condition){
          var result = 1;
          return result;
        }
        console.log(result) // undefined,变量会提升到函数体的顶部
      }
    ```

2. 不允许重复声明，重复声明会报错
3. 暂时性死区,在变量声明前访问会报错

    ```js
    function test(){
      console.log(a)// 报错
      let a = 1
    }
    ```

4. 块级作用域绑定

    ```js
    // es5创建局部作用域通过函数
    for(var i = 0; i < 10; i++) {
      setTimeout(function(){console.log(i)}, 0)
    }
    console.log(i)

    // es6，新增{}可以创建块级作用域，作用域内的变量在外部不可访问
    for(let j = 0; j < 10; j++){
      setTimeout(()=>console.log(j),0)
    }
    console.log(j)
    ```

5. `const`常用来声明被保护的变量，如常量，表示该变量不可更改
6. 在全局作用域window下，`let` `const`声明不会添加到`window`对象上，但是会屏蔽`window`对象上的同名属性或者方法。

## 字符串扩展

```js
// es5
var first_name = 'zhang', last_name = 'san';
var message = 'my name is' + first_name + last_name;
// es6新增模版字符串，解决以前字符串拼接的痛点
//const message = `my name is ${first_name}${last_name}`;
// 新增includes, startsWith, endsWidt方法

```

## 解构

es6允许我们按照一定的模式从对象或者数组中取值。

```js
// es5从对象中取值
var obj = {name: 'test', age: 12};
var name = obj.name, age = obj.age;
// es6
const {name: name2, age: age2} = obj;
```

### 对象解构

1. 基本用法

    ```js
    const {test} = {test: 'test'};

    ({test} = {test: 'new test'});// 在非变量声明的时候从对象中取值，需要使用括号包起来就
    // {test} = {test: 'new test'} // 错误的方式
    ```

2. 解构赋默认值

    ```js
    let person = {name: 'test', age: undefined};
    let {name, age=12} = person;
    console.log(name);
    console.log(age); // 对象或数组解构获取同名属性不存在或者undefined的时候，会使用默认值
    ```

3. 解构给不同的本地变量

    ```js
    let person = {name: 'test', age: undefined};
    let {name: nickName} = person;
    console.log(nickName); // type: local 表示从type取值， 在local中存储，嵌套解构的时候也是采用类似的模式
    ```

4. 嵌套解构

    ```js
    let node = {
      type: 'div',
      attrs: {
        class: 'test',
        id: 'myDiv'
      }
    };
    // 嵌套解构给不同本地变量，且提供默认值
    const {attrs: {id: elementId, class: elementClass, icon: elementIcon='none'}} = node;
    console.log(elementId);
    console.log(elementClass);
    console.log(elementIcon);
    ```

### 数组解构

数组解构同对象解构相似

```js
let [a, b] = [1, 2]
[a, b] = [2, 3]; // 此处和对象解构不同， 不必须使用括号包裹

// 使 a b 两个值互换
[a, b] = [b, a]
```

### 不完全解构

配合扩展运算符，可以实现不完全解构

```js
const arr = [1, 2, 3, 4];
const [first, ...other] = arr;
console.log(first);
console.log(other);
```

## 扩展运算符

(`...`)也叫对象扩展运算符， 用于取出对象中可遍历属性，然后拷贝到当前对象中.

```js
const arr = [1, 2, 3, 4];
const newArr = [...arr];
const obj = {name: '1'};
const newObj = {...obj};
// 利用扩展运算符可以轻松的实现对象的扩展
const base = {name: '123'};
const extra = {age: 12, name: 'new Name'};
const final = {...base, ...extra}; // 同名属性被覆盖
console.log(final);
```

## 函数扩展

### 函数参数

1. 默认参数

    ```js
    // es5
    function test(name) {
      name = name || 'default';
      console.log(name);
    }
    test()
    // es6
    function defaultName (name='default') {
      console.log(name);
    }
    defaultName()
    ```

2. 剩余参数

    ```js
    // es5不定长参数
    function test() {
      var argument = arguments;
      console.log(arguments);
    }
    // es6

    function rest(...other) {
      // 一个函数只能有一个剩余参数，且必须放在参数的最后面
      console.log(other);
    }
    ```

3. 参数解构

### 箭头函数

es6新增箭头函数，和以往的函数相比，箭头函数有以下特点：

1. 无`this` `super` `arguments`, 也没有`new.target`绑定
2. 无法使用`new`调用，箭头函数没有`[[construct]]`方法，因此不能被用为构造函数
3. 没有原型
4. 不能更改`this`, `this`在函数内部不能被更改，在函数整个生命周期保持不变
5. 没有`anguments`对象
6. 不允许重复的具名参数

```js
// 创建箭头函数

let arraowFunction = (arg1, ...rest) => {
  // 因为无法使用argument，所以传入多个可变参数的情况可以使用剩余参数来实现
};
```

### target属性

函数作为构造函数使用的时候，可以创建实例对象，此时函数被称为构造函数，为了确保构造函数被正确的调用，经常需要进行一些检测操作：

```js
function Animal() {
  if (!(this instanceof Animal)) {
    console.log(1)
    return
  }
  console.log('success');
}
Animal()
var a = new Animal();
Animal.call(a); // 无法区分

```

es6通过`new.target`进行判断， 非`new`调用的时候为`undefined`

```js
function Animal() {
  console.log(new.target === Animal);
}
Animal()
var a = new Animal();
Animal.call(a);
```

## 对象扩展

1. 初始化扩展

    ```js
    const age = 12;
    const person = {
      age, // 属性和变量同名的时候可以简写
      say () { // 方法简写
        console.log(`i am ${this.age}`);
      }
    };
    ````

2. `Object.assign`

    ```js
    // Object.assign(target, source1 [,source2 ....])
    // 签复制， 将多个source对象中的值扩展到target上
    ```

3. `Object.is`

    ```js
    // 更好的判断方法， 和===不同的是: +0 !== -0, NaN等于自身
    NaN === NaN;
    Object.is(NaN, NaN)
    ```

4. 属性名表达式

    ```js
    let a = 'custom';
    // 可以在定义对象属性的时候使用表达式语句
    let obj = {
      [a]: 123,
      ['s' + 'd']: 456
    }
    ```

5. `Object.keys` `Object.values` `Object.entries`

    ```js
    const test = {
      a: 1,
      b: 2,
      c: 3
    }
    console.log(Object.keys(test));
    console.log(Object.values(test));
    console.log(Object.entries(test));
    ```

## 类

es6支持类的语法

```js
// es6的类
class Animal {
  constructor (...rest) {
    // init
  }

  say () {
    // === Animal.prototype.say
    console.log('原型方法')
  }

  // es6的静态方法
  static category () {
    // === Animal.category
    console.log('静态方法')
  }
}

let a = new Animal ();
a.say();
//a.category();
Animal.category.call(a);


// 继承
class Dog extends Animal {
  constructor (...rest) {
    super(...rest);
    //... 相关init
    this._html = 0;
  }

  // 属性拦截访问器
  get name () {
    // do something
    return this._html
  }

  set name (val) {
    // do something
    this._html = 1
  }
}

let d = new Dog();
d.say();
// 类会继承静态方法
Dog.category();
console.log(d.name);
d.name = 12;
console.log(d.name);
```

## Symbol

es6新增符号类型, 符号类型是基本类型。

### 创建

`Symbol`没有字面量的形式，只能通过`Symbol`函数创建。

```js
let s1 = Symbol();
let s2 = Symbol();
console.log(s1 === s2);
console.log(s1);
// Symbol函数可以接收一个参数，用来描述Symbol的信息
let s3 = Symbol(122);
console.log(s3);
```

### 全局符号表

通过Symbol函数创建的Symbol值是独一无二不同的， 然而有时我们想用同一个符号值：

```js
let a = Symbol(1);
let b = Symbol(1);
console.log(a === b);
```

```js
// 方式一
// a.js
export default const SYMBOL_CONST = Symbol();
// b.js
import * from './a.js'
```

```js
// 方式二， es6有全局符号注册表，通过Symbol.for形式创建，for中接收参数，返回已存在或者新建的一个Symbol
let a = Symbol.for(1);
let b = Symbol.for(1);
console.log(a === b);
```

### 获取对象中的Symbol属性

```js
let a = Symbol();
let b = {
  a: 1,
  b: 2,
  [a]: 'symbol'
}
console.log(Object.getOwnPropertyNames(b)); // 所有属性名称， 无视其是否可枚举
console.log(Object.keys(b)); // 可枚举的属性名称
console.log(Object.getOwnPropertySymbols(b)); // 返回符号属性
```

### 内部方法的知名符号

- Symbol.hasInstance
- Symbol.iterator
- ....

## 数组扩展

### Array.of

根据传入的元素生成数组

```js
let a = Array.of(1, 2, 3)
console.log(a);
```

### Array.from

可以将类数组或者可迭代对象转换成数组

```js
let elements = document.getElementsByTagName('div');
console.log(Array.from(elements));

let b = {
  *[Symbol.iterator] () {
    for (let i = 0; i < 4; i++) {
      yield i
    }
  }
}

console.log(Array.from(b));
```

### find, findIndex

```js
let a = Array.of(1, 2, 3, 4);
console.log(a.find(item => item > 5)); // 返回第一个满足条件的值
console.log(a.findIndex(item => item > 2)); // 返回第一个满足条件的下标
```

### fill(value, start, end)

### filter

### includes

### forEach

## 迭代器和生成器

### 迭代器

实现`next`方法， 每次返回值包含`value` 和 `done` 属性， 当`done`为 `true` 的时候表示没有更多值，否则每次调用`next`都返回下一个值

```js
// 根据迭代器的定义创建一个迭代器
function createIterator(...rest) {
  let len = rest.length, i = 0;
  return {
    next () {
      if (i < len) {
        return {value: rest[i++], done: false}
      } else {
        return {value: undefined, done: true}
      }
    }
  }
}

let i = createIterator(1, 2, 3);
console.log(i.next());
console.log(i.next());
console.log(i.next());
console.log(i.next());
```

### 生成器

生成器是可以返回一个迭代器的函数

1. 创建方式

    ```js
    function *generator() {
      let i = 10;
      while (i > 0) {
        yield i;
        i--
      }
    }
    let s = generator();
    console.log(Array.from(s));
    ```

    next调用的时候传递参数

    ```js
    function *generator() {
      let i = 10;
      while (i > 0) {
        // 等式左边可以接收传入参数，增强我们控制迭代器的能力
        let result = yield i;
        console.log(result);
        i--
      }
    }
    let s = generator();
    s.next(1); // 第一次启动的时候传递参数无效， 以为没有可接收的变量
    s.next(2);
    ```

    在迭代器中使用return语句，可以提前结束迭代器

    ```js
    function *generator() {
      let i = 10;
      while (i > 0) {
        let result = yield i;
        i--
        if (result > 0) {
          return 'custom'; // 如果提供返回值，用来设置{value, done: true}中的value
        }
      }
    }
    let s = generator();
    console.log(s.next());
    console.log(s.next(2));
    console.log(s.next());
    ```

### 可迭代对象

实现了知名符号`Symbol.iteator`方法的对象

```js
let b = {
  *[Symbol.iterator] () {
    for (let i = 0; i < 4; i++) {
      yield i
    }
  }
}

console.log(Array.from(b));
```

## 异步函数

es5实现异步编程： 1. 回调； 2. 事件模型，es6 可以通过prmosie和异步函数来实现。异步函数通过`async`定义， `await`只能在异步函数内部使用，会等待异步操作执行结果，但不会阻塞主线程。

```js

function fetch() {
  return new Promise((resolve, reject)=>{
    setTimeout(()=> resolve(20), 1000);
  })
}
async function asyncF () {
  // 函数暂停执行，直到promise产生结果，继续向下执行，不会阻塞主线程
  const result = await fetch();
  console.log(result);
}

asyncF();
console.log(12);
```

## promise

Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理且更强大。

### 特点

1. 对象的状态不受外界影响 （3种状态）

    - Pending状态（进行中）
    - Fulfilled状态（已成功）
    - Rejected状态（已失败）

2. 一旦状态改变就不会再变 （两种状态改变：成功或失败）

    - Pending -> Fulfilled
    - Pending -> Rejected

### 用法

#### 创建promise

Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们是两个函数，由JavaScript引擎提供，不用自己部署。

resolve作用是将Promise对象状态由“未完成”变为“成功”，也就是Pending -> Fulfilled，在异步操作成功时调用，并将异步操作的结果作为参数传递出去；而reject函数则是将Promise对象状态由“未完成”变为“失败”，也就是Pending -> Rejected，在异步操作失败时调用，并将异步操作的结果作为参数传递出去。

```js
let promise = new Promise(function(resolve, reject){
    // ... some code
    if (/* 异步操作成功 */) {
        resolve(value);
    } else {
        reject(error);
    }
})
```

#### promise.then, promise.catch

`then`接收两个参数， 且都是可选的， `then`返回新的promise， 可以继续`then`调用

- resolve调用的函数，相关数据做为参数传入函数中
- reject使调用的函数

```js
function sleep(ms) {
    return new Promise(function(resolve, reject) {
        setTimeout(resolve, ms);
    })
}
sleep(500).then(()=> console.log("finished"));
```

`catch`相当于只传递拒绝函数给then, `then(null, ()=>{})`

#### 对promise扩展

```js
Promise.prototype.done = (success, fail) => {
  if (!fail) {
    // 提供默认的错误处理
    fail = ()=>{};
  }
  return Promise.prototype.then(success, fail);
}
```

## 代理和反射

代理允许拦截目标对象的底层操作， 拦截行为使用了一个能响应特定操作的函数（被称为陷阱）。每个代理陷阱都对应一个反射方法， 代表底层提供的默认行为的方法。

陷阱函数可接收四个参数:

- target: 接收属性的对象，即代理的目标对象
- key: 需要写入的属性
- value: 需要写入的值
- reciver: 操作发生的对象（通常是代理对象）

```js
let target = {
      name: 12
    };

let proxyTarget = new Proxy(target, {
  get(targetObj, key, value, reciver) {
    console.log(targetObj);
    console.log(key);
    console.log(value);
    console.log(reciver);
    // 对应的反射方法
    return Reflect.get(targetObj, key, value, reciver)
  },

  set (targetObj, key, value, reciver) {
    console.log(targetObj);
    console.log(key);
    console.log(value);
    console.log(reciver);
    return Reflect.set(targetObj, key, value, reciver)
  }
})

console.log(proxyTarget.name);
```

## set map weekSet weekMap

es6新增的数据解构:

- set: 不包含重复值的有序列表
- map: 键值对构成的数据结构
- weekSet, weekMap和set, map类似， 不过存储的是对象的弱引用， 便于内存优化

## 模块化

ES6 的模块自动采用严格模式，不管你有没有在模块头部加上"use strict";。

严格模式主要有以下限制。

- 变量必须声明后再使用
- 函数的参数不能有同名属性，否则报错
- 不能使用with语句
- 不能对只读属性赋值，否则报错
- 不能使用前缀0表示八进制数，否则报错
- 不能删除不可删除的属性，否则报错
- 不能删除变量delete prop，会报错，只能删除属性delete global[prop]
- eval不会在它的外层作用域引入变量
- eval和arguments不能被重新赋值
- arguments不会自动反映函数参数的变化
- 不能使用arguments.callee
- 不能使用arguments.caller
- 禁止this指向全局对象
- 不能使用fn.caller和fn.arguments获取函数调用的堆栈
- 增加了保留字（比如protected、static和interface）

上面这些限制，模块都必须遵守。由于严格模式是 ES5 引入的，不属于 ES6，所以请参阅相关 ES5 书籍，本书不再详细介绍了。

### export 命令

模块功能主要由两个命令构成：`export`和`import`。`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用`export`关键字输出该变量。下面是一个 JS 文件，里面使用export命令输出变量。

```js
// profile.js
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
```

export的写法，除了像上面这样，还有另外一种。

```js
// profile.js
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export {firstName, lastName, year};
```

需要特别注意的是，export命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系。

```js
// 报错
export 1;

// 报错
var m = 1;
export m;
```

上面两种写法都会报错，因为没有提供对外的接口。第一种写法直接输出1，第二种写法通过变量m，还是直接输出1。1只是一个值，不是接口。正确的写法是下面这样。

```js
// 写法一
export var m = 1;

// 写法二
var m = 1;
export {m};

// 写法三
var n = 1;
export {n as m};
```

上面三种写法都是正确的，规定了对外的接口m。其他脚本可以通过这个接口，取到值1。它们的实质是，在接口名与模块内部变量之间，建立了一一对应的关系。

### import 命令

使用export命令定义了模块的对外接口以后，其他 JS 文件就可以通过import命令加载这个模块。

```js
// main.js
import {firstName, lastName, year} from './profile';

// 起别名
// import { lastName as surname } from './profile';

// import * as pro from './profile'; // 整体导入， 星号（*）指定一个对象，所有输出值都加载在这个对象上面

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}
```

如果多次重复执行同一句import语句，那么只会执行一次，而不会执行多次。

```js
import 'lodash';
import 'lodash';
```

上面代码加载了两次lodash，但是只会执行一次。

```js
import { foo } from 'my_module';
import { bar } from 'my_module';

// 等同于
import { foo, bar } from 'my_module';
```

### export default 命令

`export default`命令，为模块指定默认输出。一个模块只能最多一个`default`导出

```js
// export-default.js
export default function () {
  console.log('foo');
}
```

上面代码是一个模块文件export-default.js，它的默认输出是一个函数,其他模块加载该模块时，import命令可以为该匿名函数指定任意名字。

```js
// import-default.js
import customName from './export-default';
customName(); // 'foo'
```

本质上，export default就是输出一个叫做default的变量或方法，然后系统允许你为它取任意名字。所以，下面的写法是有效的。

```js
// modules.js
function add(x, y) {
  return x * y;
}
export {add as default};
// 等同于
// export default add;

// app.js
import { default as xxx } from 'modules';
// 等同于
// import xxx from 'modules';
```

正是因为export default命令其实只是输出一个叫做default的变量，所以它后面不能跟变量声明语句。

```js
// 正确
export var a = 1;

// 正确
var a = 1;
export default a;

// 错误
export default var a = 1;
```

上面代码中，export default a的含义是将变量a的值赋给变量default。所以，最后一种写法会报错。
