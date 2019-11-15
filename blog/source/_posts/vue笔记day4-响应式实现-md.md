---
title: vue笔记day4-响应式实现.md
date: 2019-04-22 09:33:58
tags: vue
---

## vue响应式的实现原理

vue通过Object.defineProperty将对象设置成为响应式，通过Dep收集依赖，在数据发生变化的时候通知订阅的watcher进行更新。

### 响应式对象 

Object.defineProperty(obj, prop, descriptor)可以定义属性的get和set方法，用来检测数据访问和变动

```
    var obj = {}, val = 'custom';
    Object.defineProperty(obj,'name', {
      configurable: true,
      enumerable: true,
      get: function () {
        return val
      },
      set: function (newVal) {
        val = newVal
      }
    })
```


### Dep和Watcher

dep是依赖收集器，有三个基本方法

```
class Dep {
    /*
     * 某一个主体对象的依赖收集器，可以有多个观察者对其感兴趣，
     * 实际上是defineReactive中data[key]抽象出来的管理类，用来处理依赖，减少耦合
     * 发布订阅的变体，data可以发布，可以订阅
     * 当主体发生改变的时候，可以通知相对应的观察者更新
     * 因此其有三个基本的方法，添加观察者，移除观察者，提醒更新
     */
    constructor () {
        this.subs = [];
    }
    addSub (watcher) {
        if (this.subs.indexOf(watcher) === -1) {
            // 避免重复添加
            this.subs.push(watcher);
        }
    }
    removeSub (watcher) {
        this.subs.splice(this.subs.indexOf(watcher), 1);
    }
    notify () {
        console.log(this.subs);
        this.subs.forEach(sub => sub.update());
    }
    depend () {
        /*
         * 通过target，来建立Watcher和Dep之间的联系，将watcher添加到subs数组中去
         */
        if (Dep.target) {
            Dep.target.addDep(this);
        }
    }
}
// 通过此属性，标示访问为watcher访问，用来收集依赖
Dep.target = null
class Watcher {
    /*
     * 观察者，对某一目标主体感兴趣，在目标主体发生变化的时候会update
     */
    constructor (obj, expOrFn, cb) {
        /** expOrFn: a.b.c或者函数的情况 */
        this.obj = obj;
        this.getter = parsePath(expOrFn)
        this.get();
        this.cb = cb
    }
    update () {
        if (this.cb) this.cb.call(this.obj, this.obj)
    }
    addDep (dep) {
        dep.addSub(this);
    }
    get () {
        Dep.target = this;
        let val = this.getter.call(this.obj, this.obj)
        Dep.target = null;
        return val;
    }
}

```

### 模拟实现vue响应式

```
data = {
  name: "hello, world",
  obj: {
    name: "obj",
    message: "msg"
  }
};
// 定义重新定义get, set使对象变成响应式
function defineReactive(obj, key, value) {
  if (typeof value == "object"){
    observe(value)
  }
  // 这样每一个数据都有自己的dep，当数据改动放的时候，就可以通知dep中的订阅者update
  var dep = new Dep()
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function () {
      console.log(Dep.target);
      console.log("get");
      // 存在target的时候是target调用，需要添加依赖
      if (Dep.target) {
        // console.log(this);
        // dep.addSub(Dep.target)
        dep.depend()
      }
      return value
    },
    set: function (newVal) {
      console.log('set');
      if (value != newVal) {
        value = newVal;
        dep.notify()
      }
    }
  })
}
function Observe(value) {
  var keys = Object.keys(value);
  for(var i=0; i<keys.length; i++){
    defineReactive(value, keys[i], value[keys[i]])
  }
}
function observe(value) {
  return new Observe(value)
}
// 收集数据的变化, 当数据变化的时候触发notify
function Dep() {
  this.subs = [];
  this.addSub = function (sub) {
    this.subs.push(sub)
  };
  this.notify = function () {
    this.subs.forEach(function (sub) {
      sub.update();
    })
  };
  // target来确定是否存在watcher,普通的获取并不会添加依赖
  this.target = null;
  this.depend = function(){
    if (Dep.target) {
      // dep.depend在defineReactive中由dep调用，所以这里的this指向dep实例
      // dep,target是Watcher实例，因此Watcher中就可以进一步判断，
      // 并且在dep中添加watcher订阅
      Dep.target.addDep(this)
    }
  }

}
// watcher
function Watcher(obj,expression, cb) {
  // 由dep实例调用
  this.addDep = function (dep) {
    dep.addSub(this)
  };
  this.get = function () {
    Dep.target = this;
    var val = this.obj[expression]
    Dep.target = null;
    return val
  };

  // 触发一次get，将watcher添加到dep中
  this.obj = obj;
  this.value = this.get();
  this.update = function () {
    console.log("updated");
    cb.call(obj, obj)
  };

}
observe(data);

function cb() {
  console.log("cb is called")
}
function cb2() {
  console.log('cb2')
}
// 会出现重复添加
/*
vue中的处理
addDep (dep: Dep) {
   const id = dep.id
  // 已经添加过就不会重复添加
   if (!this.newDepIds.has(id)) {
     this.newDepIds.add(id)
     this.newDeps.push(dep)
     if (!this.depIds.has(id)) {
       dep.addSub(this)
     }
   }
}
*/

new Watcher(data, 'name', cb);
new Watcher(data, 'name', cb);
new Watcher(data.obj, 'name', cb2)
```
