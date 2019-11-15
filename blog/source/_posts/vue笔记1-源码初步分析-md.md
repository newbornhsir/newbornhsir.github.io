---
title: vue笔记1-源码初步分析.md
date: 2018-03-08 21:58:51
tags: vue
categories: vue
---

### 全局注册

```
(function (global,factory){
    global.Vue = factory()
}(this,(function(){
    ...
    function Vue$3(options){
        this._init(options)
    }
    /*
     *initMixin中为Vue$3的原型添加了_init方法
     */
    initMixin(Vue$3);
    /*
     * 在Vue$3的原型上定义$data,$props,$set,$delete,$watch
     */
    stateMixin(Vue$3);
    // 事件相关
    /*
     * 在原型上定义$on,$once,$off,$emit,
     */
    eventsMixin(Vue$3);
    // 生命周期
    /*
     * 原型上定义_update,$forceUpdate,$destory,
     * 
     */
    lifecycleMixin(Vue$3);
    // render处理
    /*
     * 原型上绑定一些辅助方法和
     * $nextTick,_render
     */
    renderMixin(Vue$3);
    ....
    /*
     * Vue$3上添加了config,set,delete,nextTick,options
     * options中添加了components,directives,filters对象
     */
    initGlobalAPI(Vue$3);
    // Vue$3原型添加$isServer属性
    Object.defineProperty(Vue$3.prototype, '$isServer', {
      get: isServerRendering
    });
    // Vue$3原型添加$ssrContext属性
    Object.defineProperty(Vue$3.prototype, '$ssrContext', {
      get: function get () {
        /* istanbul ignore next */
        return this.$vnode && this.$vnode.ssrContext
      }
    });
    Vue$3.version = '2.5.13';
    ....
    // install platform specific utils
    Vue$3.config.mustUseProp = mustUseProp;
    Vue$3.config.isReservedTag = isReservedTag;
    Vue$3.config.isReservedAttr = isReservedAttr;
    Vue$3.config.getTagNamespace = getTagNamespace;
    Vue$3.config.isUnknownElement = isUnknownElement;

    // install platform runtime directives & components
    extend(Vue$3.options.directives, platformDirectives);
    extend(Vue$3.options.components, platformComponents);

    // install platform patch function
    Vue$3.prototype.__patch__ = inBrowser ? patch : noop;

    // public mount method
    // Vue添加$mount,Vue.$options添加render
    Vue$3.prototype.$mount = function (
      el,
      hydrating
    ) {
      el = el && inBrowser ? query(el) : undefined;
      return mountComponent(this, el, hydrating)
    };
    Vue$3.nextTick(function () {
      .....
    }, 0);
    ...
    /*
     * 重写$mount方法
     */
    var mount = Vue$3.prototype.$mount;
    Vue$3.prototype.$mount = function (
      el,
      hydrating
    ) {
      ...
      return mount.call(this, el, hydrating)
    };

      Vue$3.compile = compileToFunctions;
    ....
    return Vue$3
})))
```

### 实例化过程

以`var vm = new Vue({el: '#app',data:{name: 'demo'}})`为例：


- 第一步，调用构造函数

```
function Vue$3 (options) {
  /*
  * 检测Vue的调用方式和环境
  */
  if ("development" !== 'production' &&
    !(this instanceof Vue$3)
  ) {
    warn('Vue is a constructor and should be called with the `new` keyword');
  }
  // 调用 _init方法
  this._init(options);
}

```

- 第二步：执行初始化函数

```
function initMixin (Vue) {
  Vue.prototype._init = function (options) {
    var vm = this;// 这里的Vue$3的实例化对象
    // a uid
    vm._uid = uid$1++;

    var startTag, endTag;
    /* istanbul ignore if */
    if ("development" !== 'production' && config.performance && mark) {
      startTag = "vue-perf-start:" + (vm._uid);
      endTag = "vue-perf-end:" + (vm._uid);
      mark(startTag);
    }

    // a flag to avoid this being observed
    // 避免被观察
    vm._isVue = true;
    // merge options
    // 实例化的时候没有_isComponent,所以第一次不执行这里
    if (options && options._isComponent) {
      // optimize internal component instantiation
      // since dynamic options merging is pretty slow, and none of the
      // internal component options needs special treatment.
      initInternalComponent(vm, options);
    } else {
      //resolveConstructorOptions(vm.constructor),返回Vue$3的默认配置
      // 实例化对象赋值$options
      
      vm.$options = mergeOptions(
        resolveConstructorOptions(vm.constructor),
        options || {},
        vm
      );
    }
    /* istanbul ignore else */
    {
      initProxy(vm);
    }
    // return;
    // expose real self
    vm._self = vm;
    /*
     * 初始花生命周期
     * $parent,$root,$children,$refs,_watcher,_inactive
     * _directInactive,_isMounted,_isDestroyed,_isBegingDestroyed
     */
    initLifecycle(vm);
    // 事件的监听和传递
    /* 
     * _events ,_hasHoolEvent
     */
    initEvents(vm);
    /*
     * _vnode,_staticTrees,$slots,$scopedSlots
     * _c,$createElemen
     */
    initRender(vm);

    // 检查beforeCreate生命周期函数
    callHook(vm, 'beforeCreate');
    // 处理inject
    initInjections(vm); // resolve injections before data/props
    // 初始话状态, method.data,props
    initState(vm);
    // provide的处理
    initProvide(vm); // resolve provide after data/props
    callHook(vm, 'created');

    /* istanbul ignore if */
    if ("development" !== 'production' && config.performance && mark) {
      vm._name = formatComponentName(vm, false);
      mark(endTag);
      measure(("vue " + (vm._name) + " init"), startTag, endTag);
    }

    if (vm.$options.el) {
        /*
         * 渲染
         */
      vm.$mount(vm.$options.el);
    }
  };
}

```

- 第三步：执行$mount方法

```
var mount = Vue$3.prototype.$mount;
Vue$3.prototype.$mount = function (// 重写$mount方法
  el,
  hydrating
) {
  // 根结点
  el = el && query(el);

  /* istanbul ignore if */
  // 不能为body的提示
  if (el === document.body || el === document.documentElement) {
    "development" !== 'production' && warn(
      "Do not mount Vue to <html> or <body> - mount to normal elements instead."
    );
    return this
  }
  var options = this.$options;
  // resolve template/el and convert to render function
  if (!options.render) {
    /*
     * 第一次实例化的时候没有render
     */
    var template = options.template;
    if (template) {
        // 以template形式传入的dom
      if (typeof template === 'string') {
        if (template.charAt(0) === '#') {
          template = idToTemplate(template);
          /* istanbul ignore if */
          if ("development" !== 'production' && !template) {
            warn(
              ("Template element not found or is empty: " + (options.template)),
              this
            );
          }
        }
      } else if (template.nodeType) {
        template = template.innerHTML;
      } else {
        {
          warn('invalid template option:' + template, this);
        }
        return this
      }
    } else if (el) {
        // 获取dom的html内容
      template = getOuterHTML(el);
    }
    if (template) {
      /* istanbul ignore if */
      if ("development" !== 'production' && config.performance && mark) {
        mark('compile');
      }
      var ref = compileToFunctions(template, {
        shouldDecodeNewlines: shouldDecodeNewlines,
        shouldDecodeNewlinesForHref: shouldDecodeNewlinesForHref,
        delimiters: options.delimiters,
        comments: options.comments
      }, this);
      var render = ref.render;
      var staticRenderFns = ref.staticRenderFns;
      /*
       * vm.$options提供render，staticRenderFns方法
       */
      options.render = render;
      options.staticRenderFns = staticRenderFns;

      /* istanbul ignore if */
      if ("development" !== 'production' && config.performance && mark) {
        mark('compile end');
        measure(("vue " + (this._name) + " compile"), 'compile', 'compile end');
      }
    }
  }
  /*
   * mount 方法：return mountComponent(this, el, hydrating)
   */
  return mount.call(this, el, hydrating)
};
```

- 第四步：执行`mountComponent`


