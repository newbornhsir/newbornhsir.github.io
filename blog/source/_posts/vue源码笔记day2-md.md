---
title: vue源码笔记day2-模版编译.md
date: 2018-03-12 21:47:19
tags:
---

vue实例化的时候会调用`_init()`方法，文件在`src/core/instance/init.js`,在`_init`主要做了以下几件事：

- 合并options
- 初始化实例对象
- 挂载

首先看看`$options`: 

```
    vm.$options = mergeOptions(
        resolveConstructorOptions(vm.constructor),
        options || {},
        vm
    )
```

在 `resolveConstructorOptions` 中：

```
export function resolveConstructorOptions (Ctor: Class<Component>) {
  // 在第一次实例化的时候返回了Vue的options,
  // if 循环
  let options = Ctor.options
  if (Ctor.super) {
    const superOptions = resolveConstructorOptions(Ctor.super)
    const cachedSuperOptions = Ctor.superOptions
    if (superOptions !== cachedSuperOptions) {
      // super option changed,
      // need to resolve new options.
      Ctor.superOptions = superOptions
      // check if there are any late-modified/attached options (#4976)
      const modifiedOptions = resolveModifiedOptions(Ctor)
      // update base extend options
      if (modifiedOptions) {
        extend(Ctor.extendOptions, modifiedOptions)
      }
      options = Ctor.options = mergeOptions(superOptions, Ctor.extendOptions)
      if (options.name) {
        options.components[options.name] = Ctor
      }
    }
  }
  return options
}

```

循环中的东西暂时不清楚，待后面再回头看，暂时知道返回了`Vue.options`。

在 `mergeOptions`中对两个option进行合并，返回一个新的option就是vm.$options。在合并的过程中，还对指令，prop等以及冲突的key进行了处理。 

接下来是初始化，然后调用`vm.$mount(vm.$options.el)`


$mount定义在`src/platform/runtime/index.js`中：

```
Vue.prototype.$mount = function (
  el?: string | Element,
  hydrating?: boolean
): Component {
  el = el && inBrowser ? query(el) : undefined
  return mountComponent(this, el, hydrating)
}

```

为了实现模版编译，重写了$mount方法

```
// 保存之前的$mount,在模版编译完成后调用
const mount = Vue.prototype.$mount
// 为vue添加模版编译的功能
Vue.prototype.$mount = function (
  el?: string | Element,
  hydrating?: boolean
): Component {
  el = el && query(el)

  /* istanbul ignore if */
  if (el === document.body || el === document.documentElement) {
    process.env.NODE_ENV !== 'production' && warn(
      `Do not mount Vue to <html> or <body> - mount to normal elements instead.`
    )
    return this
  }

  const options = this.$options
  // resolve template/el and convert to render function
  // 处理template,编译成render函数，render函数存在的情况会优先使用render函数
  if (!options.render) {
    // 检查是否存在template，不存在取outerHTML
    let template = options.template
    if (template) {
      if (typeof template === 'string') {
        if (template.charAt(0) === '#') {
          template = idToTemplate(template)
          /* istanbul ignore if */
          if (process.env.NODE_ENV !== 'production' && !template) {
            warn(
              `Template element not found or is empty: ${options.template}`,
              this
            )
          }
        }
      } else if (template.nodeType) {
        template = template.innerHTML
      } else {
        if (process.env.NODE_ENV !== 'production') {
          warn('invalid template option:' + template, this)
        }
        return this
      }
    } else if (el) {
      template = getOuterHTML(el)
    }
    if (template) {
      /* istanbul ignore if */
      if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
        mark('compile')
      }
      /*
       * 编译render和staticRenderFns函数
       * 这里主要进行了三种操作
       * 1. 获取template，转换成ast树
       * 2. ast树转换成 code， code类似_c('div',{attrs:{"id":"app"}}....")])的字符串，这里的_c_e,
       *    在renderMixin中_c,_e之类的方法已经添加在Vue的原型中了
       * 3. new Function(code)创建render函数，staticRenderFns函数的创建
       */
      const { render, staticRenderFns } = compileToFunctions(template, {
        shouldDecodeNewlines,
        shouldDecodeNewlinesForHref,
        delimiters: options.delimiters,
        comments: options.comments
      }, this)
      options.render = render
      options.staticRenderFns = staticRenderFns

      /* istanbul ignore if */
      if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
        mark('compile end')
        measure(`vue ${this._name} compile`, 'compile', 'compile end')
      }
    }
  }
  return mount.call(this, el, hydrating)
}

```

从代码可看出，主要的编译工作都在 compileToFunctions 中进行，compileToFunctions是创建的模版编译器中的一个方法，在创建的编译器中，有compile和compileToFunctions两个方法：


```
// 创建的编译器对象返回了两个方法
const { compile, compileToFunctions } = createCompiler(baseOptions)

export const createCompiler = createCompilerCreator(function baseCompile (
  template: string,
  options: CompilerOptions
): CompiledResult {
  /*
   * 生成ast树
   */
  const ast = parse(template.trim(), options)
  // ast进行优化，标记静态节点，执行optimiza后,ast有了static,staticRoot属性
  if (options.optimize !== false) {
    optimize(ast, options)
  }
  // 根据ast树生成所需要的code,code包含render和staticRenderFns
  // code用来动态创建render和staticRenderFns函数
  const code = generate(ast, options)
  return {
    ast,
    render: code.render,
    staticRenderFns: code.staticRenderFns
  }
})

export function createCompilerCreator (baseCompile: Function): Function {
  return function createCompiler (baseOptions: CompilerOptions) {
    function compile (
      template: string,
      options?: CompilerOptions
    ): CompiledResult {
      const finalOptions = Object.create(baseOptions)
      const errors = []
      const tips = []
      finalOptions.warn = (msg, tip) => {
        (tip ? tips : errors).push(msg)
      }
      // merge,形成finalOptions
      if (options) {
        // merge custom modules
        if (options.modules) {
          finalOptions.modules =
            (baseOptions.modules || []).concat(options.modules)
        }
        // merge custom directives
        if (options.directives) {
          finalOptions.directives = extend(
            Object.create(baseOptions.directives || null),
            options.directives
          )
        }
        // copy other options
        for (const key in options) {
          if (key !== 'modules' && key !== 'directives') {
            finalOptions[key] = options[key]
          }
        }
      }
      // 基础的模版编译，返回编译的结果
      const compiled = baseCompile(template, finalOptions)
      if (process.env.NODE_ENV !== 'production') {
        errors.push.apply(errors, detectErrors(compiled.ast))
      }
      compiled.errors = errors
      compiled.tips = tips
      return compiled
    }

    return {
      compile,
      compileToFunctions: createCompileToFunctionFn(compile)
    }
  }
}

export function createCompileToFunctionFn (compile: Function): Function {
  // 创建一个对象，用来处理缓存
  const cache = Object.create(null)

  return function compileToFunctions (
    template: string,
    options?: CompilerOptions,
    vm?: Component
  ): CompiledFunctionResult {
    options = extend({}, options)
    const warn = options.warn || baseWarn
    delete options.warn

    /* istanbul ignore if */
    if (process.env.NODE_ENV !== 'production') {
      // detect possible CSP restriction
      try {
        new Function('return 1')
      } catch (e) {
        if (e.toString().match(/unsafe-eval|CSP/)) {
          warn(
            'It seems you are using the standalone build of Vue.js in an ' +
            'environment with Content Security Policy that prohibits unsafe-eval. ' +
            'The template compiler cannot work in this environment. Consider ' +
            'relaxing the policy to allow unsafe-eval or pre-compiling your ' +
            'templates into render functions.'
          )
        }
      }
    }

    // check cache
    // 检查缓存，有缓存的情况直接读取缓存
    const key = options.delimiters
      ? String(options.delimiters) + template
      : template
    if (cache[key]) {
      return cache[key]
    }

    // compile
    // 编译
    const compiled = compile(template, options)

    // check compilation errors/tips
    if (process.env.NODE_ENV !== 'production') {
      if (compiled.errors && compiled.errors.length) {
        warn(
          `Error compiling template:\n\n${template}\n\n` +
          compiled.errors.map(e => `- ${e}`).join('\n') + '\n',
          vm
        )
      }
      if (compiled.tips && compiled.tips.length) {
        compiled.tips.forEach(msg => tip(msg, vm))
      }
    }

    // turn code into functions
    const res = {}
    const fnGenErrors = []
    // 将render转换成Function对象
    res.render = createFunction(compiled.render, fnGenErrors)
    // 将staticRenderFns转换成Function对象
    res.staticRenderFns = compiled.staticRenderFns.map(code => {
      return createFunction(code, fnGenErrors)
    })

    // check function generation errors.
    // this should only happen if there is a bug in the compiler itself.
    // mostly for codegen development use
    /* istanbul ignore if */
    if (process.env.NODE_ENV !== 'production') {
      if ((!compiled.errors || !compiled.errors.length) && fnGenErrors.length) {
        warn(
          `Failed to generate render function:\n\n` +
          fnGenErrors.map(({ err, code }) => `${err.toString()} in\n\n${code}\n`).join('\n'),
          vm
        )
      }
    }
    // 保存缓存并返回
    return (cache[key] = res)
  }
}

```

由此可见，compile用来merge options,生成和优化ast树，然后将其转换为code,而compileToFunctions则是检查缓存，如果不存在缓存则调用compile函数，生成code并将其转化成渲染函数，缓存并返回。所以使用render和staticRenderFns有了缓存和静态节点的处理，因而效率更高吧。

vue模版编译:
1. 编译器对象
- compile
    1. 合并options
    2. 调用baseCompile形成ast,code
- compileToFunctions
    1. 检查缓存，如果存在缓存返回缓存结果
    2. 编译
    3. 将编译结果构造成render和staticRenderFns函数，缓存并返回




