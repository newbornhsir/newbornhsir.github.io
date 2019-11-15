---
title: observer.md
date: 2018-03-25 19:15:56
tags: javascript 设计模式
---

### 发布订阅

发布者是实现了添加订阅、移除订阅、发布订阅的对象

```
/**新闻发布者， 
 * publisher 可以发布消息，让其它用户订阅消息，移除订阅
 */
const newsPublisher = {
    subs: {
        base: []
    },
    subscribe (newsType, cb) {
        if (!this.subs[newsType]) {
            /**订阅一个新的新闻类型 */
            this.subs[newsType] = []
        }
        this.subs[newsType].push(cb)
    },
    unSubscribe(newsType, cb) {
        /**取消订阅 */
        let cbs = this.subs[newsType]
        if (cbs) {
            let index = cbs.indexOf(cb)
            cbs.splice(index,1)
        }
    },
    publisher (newsType) {
        let cbs = this.subs[newsType]
        if (cbs) {
            cbs.forEach(cb => cb())
        }
    }
}

class Person {
    constructor (cb) {
        this.cb = cb
    }
    subscribe (newType) {
        newsPublisher.subscribe(newType, this.cb)
    }
    unSubscribe (newType) {
        newsPublisher.unSubscribe(newType, this.cb)
    }
}

p1 = new Person(()=>console.log('p1 know'))
p2 = new Person(()=>console.log('p2 know'))
p1.subscribe('base')
p2.subscribe('base')
newsPublisher.publisher('base')
p2.unSubscribe('base')
newsPublisher.publisher('base')
```

### 观察者模式

观察者模式是发布订阅的变体。将发布者所基本的添加、移除订阅，发布订阅的功能抽象出一个类，叫做依赖管理器，而订阅这部分的对象抽象出update方法，叫做watcher，在依赖出发notify的时候进行更新

```
 /**
  * 现在存在多个publisher，都可以添加订阅，移除订阅，发布订阅，
  * 这部分公用的抽象出一个类，叫做Dep，依赖管理器
  * 可以订阅的用户叫做watcher
  */
 class Dep {
     constructor () {
         this.sub = []
     }
     addSub (watcher) {
        /**添加 */
        let index = this.sub.indexOf(watcher)
        if (index === -1) {
            this.sub.push(watcher)
        }
     }
     removeSub () {
        let index = this.sub.indexOf(watcher)
        if (index !== -1) {
            this.sub.splice(index, 1)
        }
     }
     notify () {
         this.sub.forEach(sub => sub.update())
     }
 }

 class Watcher {
     constructor () {
        
     }
     update () {

     }
     addDep (dep) {
         dep.addSub(this)
     }
 }
```