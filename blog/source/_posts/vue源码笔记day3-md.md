---
title: vue源码笔记day3-渲染.md
date: 2018-03-22 21:57:47
tags: vue
---

在vue完成模版编译的时候，是如何渲染的呢？


```

// $mount

Vue.prototype.$mount = function (
  el?: string | Element,
  hydrating?: boolean
): Component {
  el = el && inBrowser ? query(el) : undefined
  return mountComponent(this, el, hydrating)
}


```


```

export function mountComponent (
  vm: Component,
  el: ?Element,
  hydrating?: boolean
): Component {
  // 实例添加$el属性,根结点
  vm.$el = el
  // render的检测，此时已经编译完成
  if (!vm.$options.render) {
    //
    vm.$options.render = createEmptyVNode
    if (process.env.NODE_ENV !== 'production') {
      .....
      }
    }
  }
  // 检测是否有beforemount生命周期钩子的调用
  callHook(vm, 'beforeMount')
  // 创建更新函数
  let updateComponent
  /* istanbul ignore if */
  if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
    updateComponent = () => {
      .....
    }
  } else {
    updateComponent = () => {
      vm._update(vm._render(), hydrating)
    }
  }

  // we set this to vm._watcher inside the watcher's constructor
  // since the watcher's initial patch may call $forceUpdate (e.g. inside child
  // component's mounted hook), which relies on vm._watcher being already defined
  // watcher如何进行数据检测？
  new Watcher(vm, updateComponent, noop, null, true /* isRenderWatcher */)
  hydrating = false

  // manually mounted instance, call mounted on self
  // mounted is called for render-created child components in its inserted hook
  // 挂载完成
  if (vm.$vnode == null) {
    vm._isMounted = true
    callHook(vm, 'mounted')
  }
  return vm
}


```

调用Watcher

```

```
