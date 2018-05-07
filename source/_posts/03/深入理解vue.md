---
title: 深入理解vue
date: 2018-03-24 11:05:17
tags: vue
header-img: "head.jpg"
---

## 写在前面

### vue与juqery的区别 
通过TODO List实现的例子总结得出
1.数据视图分离，解耦（开放封闭原则：对扩展开放，对修改封闭）。
2.数据驱动视图，只关心数据变化，dom操作被封装。

### MVC
- View 传送指令到 Controller
- Controller 完成业务逻辑后，要求 Model 改变状态
- Model 将新的数据发送到 View，用户得到反馈

View了解Controller，Controller了解Model，而View能够直接访问Model。
例如用户点击了一个按钮（操作了视图），然后controller进行业务逻辑操作，最后将操作结果发送给视图。
!['mvc'](mvc.png)

### MVVM
- M 数据，对应于vue中的data
- V 视图，对应于DOM
- VM 视图模型，对应于vue实例

视图通过事件绑定，经过视图模型操作数据，数据通过数据绑定，经过视图模型修改视图
!['vue'](vue.png)

### 源码分析思路过程
首先文档，然后通过一个简单的例子开始，从入口文件一步步分析，抓住主线。
vue组件通常都有一个install方法，用于将组件装在到vue对象上。

## vue源码分析
推荐文章 [Vue源码学习](http://hcysun.me/2017/03/03/Vue%E6%BA%90%E7%A0%81%E5%AD%A6%E4%B9%A0/)

### vue三要素
- 响应式：监听数据变化。
- 模板引擎：解析模板指令。
- 渲染：模板结合modal如何生成DOM，当产生变化时更新DOM。

### vue构造函数
整体流程如下：
1、Vue.prototype 下的属性和方法的挂载主要是在src/core/instance目录中的代码处理的。
2、Vue下的静态属性和方法的挂载主要是在src/core/global-api目录下的代码处理的。
3、web-runtime.js主要是添加web平台特有的配置、组件和指令，web-runtime-with-compiler.js给Vue的$mount方法添加compiler编译器，支持template。

#### 初始化，绑定原型对象属性
定义 Vue 构造函数，然后以Vue构造函数为参数，调用了五个方法，最后导出 Vue。这些方法的作用，就是在 Vue 的原型 prototype 上挂载方法或属性。
```
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}

// initMixin(Vue) 初始化入口    src/core/instance/init.js
// stateMixin(Vue) 数据绑定，$watch方法   src/core/instance/state.js
// renderMixin(Vue) 渲染方法，用来生成render function 和 VNode   src/core/instance/render.js 
// eventsMixin(Vue) 事件方法，比如$on,$off,$emit   src/core/instance/events.js 
// lifecycleMixin(Vue) 生命周期方法   src/core/instance/lifecycle.js
```

#### 绑定静态属性和方法
导入上文导出的Vue（已经在原型上挂载了方法和属性），将Vue作为参数传给initGlobalAPI ，最后又在 Vue.prototype上挂载了 $isServer，在Vue上挂载了version属性。

#### 安装指令和组件
覆盖Vue.config的属性，将其设置为平台特有的一些方法。Vue.options.directives和Vue.options.components安装平台特有的指令和组件。在Vue.prototype上定义`__patch__`和 `$mount`。

#### 覆盖$mount,编译模板函数
缓存来自web-runtime.js文件的$mount函数，然后覆盖覆盖了Vue.prototype.$mount。在Vue上挂载compile，compileToFunctions函数的作用，就是将模板template编译为render函数。

#### 整体来看执行顺序
```
initLifecycle(vm)
initEvents(vm)
callHook(vm, 'beforeCreate')
initProps(vm)
initMethods(vm)
initData(vm)
initComputed(vm)
initWatch(vm)
callHook(vm, 'created')
initRender(vm)
```

### 数据响应系统
模型通过Observer、Dep、Watcher、Directive等一系列对象的关联，最终和视图建立起关系。
推荐文章[Vue 源码解析：深入响应式原理](https://github.com/DDFE/DDFE-blog/issues/7)
![vue数据响应系统](vue1.jpg)
- 通过 Observer 对 data 做监听，并且提供了订阅某个数据项变化的能力。
- 把 template 编译成一段 document fragment，然后解析其中的 Directive，得到每一个 Directive 所依赖的数据项和update方法。
- 通过Watcher把上述两部分结合起来，即把Directive中的数据依赖通过Watcher订阅在对应数据的 Observer 的 Dep 上。当数据变化时，就会触发 Observer 的 Dep 上的 notify 方法通知对应的 Watcher 的 update，进而触发 Directive 的 update 方法来更新 DOM 视图，最后达到模型和视图关联起来。

通过一个简单的例子来看一下
```
var data = {
  name: 'chenjp',
  age: 25,
  school: {
    text: 'NCU',
    major: 'computer'
  }
}

observer(data)

new Watch('name', function (oldVal, newVal) {
  console.log('name changed', oldVal, newVal)
  console.log(data) // 这里data还是原来的，没变
})

function Watch(exp, fn) {
  this.exp = exp
  this.fn = fn
  pushTarget(this)
  data[exp] // 触发get，与observer关联
}

// 依赖管理
function Dep() {
  this.subs = []
  this.depend = function () {
    this.subs.push(Dep.target)
  }
  this.notify = function (oldVal, newVal) {
    for (let i = 0; i < this.subs.length; i++) {
      this.subs[i].fn(oldVal, newVal)
    }
  }
}

Dep.target = null

function pushTarget(watch) {
  Dep.target = watch
}

//将数据对象data的属性转换为访问器属性
function observer(thisData) {
  if (Object.prototype.toString.call(thisData) === '[object Object]') {
    for (let item in thisData) {
      let dep = new Dep()
      let val = thisData[item]
      observer(val)
      Object.defineProperty(thisData, item, {
        enumerable: true,
        configurable: true,
        get: function () {
          // 添加依赖
          dep.depend()
          return val
        },
        set: function (newVal) {
          if (val === newVal) {
            return
          }
          observer(newVal)
          // 触发依赖
          dep.notify(val, newVal)
        }
      })
    }
  } else {
    return
  }
}
```

### 渲染与重新渲染
模板->render函数->DOM。将template编译成render函数，这个函数返回一个虚拟dom节点，最终渲染成dom。
当render执行的时候，所依赖的变量就会被求值，并被收集为依赖。按照Vue中 watcher.js 的逻辑，当依赖的变量有变化时不仅仅回调函数被执行，实际上还要重新求值，即还要执行一遍。

vm_render 方法最终返回一个 vnode 对象，即虚拟DOM，然后作为 vm_update 的第一个参数传递了过去，我们看一下 vm_update 的逻辑：如果还没有 prevVnode 说明是首次渲染，直接创建真实DOM。如果已经有了 prevVnode 说明不是首次渲染，那么就调用patch函数，patch函数用新的vnode和老的vnode进行diff，最后完成dom的更新工作。这就是Vue更新DOM的逻辑。

### vue数据绑定预渲染总结
实例化一个watcher，在求值的过程中this.value = this.lazy ? undefined : this.get()，会调用this.get()方法，因此在实例化的过程当中Dep.target会被设为这个watcher，通过调用vm._render()方法生成新的Vnode并进行diff的过程中完成了模板当中变量依赖收集工作。即这个watcher被添加到了在模板当中所绑定变量的依赖当中。一旦model中的响应式的数据发生了变化，这些响应式的数据所维护的dep数组便会调用dep.notify()方法完成所有依赖遍历执行的工作，这里面就包括了视图的更新即。

![](vueimgdetail.png)

### 总结：vue整个的实现流程
第一步：解析模板成render函数
第二步：响应式开始监听
第三步：首次渲染，显示页面，且绑定依赖
第四部：data属性变化，触发set监听执行updateComponent方法，然后触发rernder函数（render函数再次执行，重新patch）

## 虚拟DOM
vdom很好的将dom做了一层映射关系
vdom完全是用js去实现，和宿主浏览器没有任何联系
这里就分享两篇写的非常好的文章吧
推荐文章[Vue 2.0 的 virtual-dom 实现简析](https://github.com/DDFE/DDFE-blog/issues/18),[深入 Vue2.x 的虚拟 DOM diff 原理](https://cloud.tencent.com/developer/article/1006029),[从 $mount 讲起，一起探究Vue的渲染机制](https://segmentfault.com/a/1190000009467029)

总结：
在initRender(vue构造函数中的五个方法中最后一个)中如果有 vm.$options.el 还要调用 vm.$mount(vm.$options.el)，实际上是调用了src/core/instance/lifecycle.js中的mountComponent方法，mountComponent函数如下
```
export function mountComponent (
  vm: Component,
  el: ?Element,
  hydrating?: boolean
): Component {
  // vm.$el为真实的node
  vm.$el = el
  // 如果vm上没有挂载render函数
  if (!vm.$options.render) {
    // 空节点
    vm.$options.render = createEmptyVNode
  }
  // 钩子函数
  callHook(vm, 'beforeMount')

  let updateComponent
  /* istanbul ignore if */
  if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
    ...
  } else {
    // updateComponent为监听函数, new Watcher(vm, updateComponent, noop)
    updateComponent = () => {
      // Vue.prototype._render 渲染函数
      // vm._render() 返回一个VNode
      // 更新dom
      // vm._render()调用render函数，会返回一个VNode，在生成VNode的过程中，会动态计算getter,同时推入到dep里面
      vm._update(vm._render(), hydrating)
    }
  }

  // 新建一个_watcher对象
  // vm实例上挂载的_watcher主要是为了更新DOM
  // vm/expression/cb
  vm._watcher = new Watcher(vm, updateComponent, noop)
  hydrating = false

  // manually mounted instance, call mounted on self
  // mounted is called for render-created child components in its inserted hook
  if (vm.$vnode == null) {
    vm._isMounted = true
    callHook(vm, 'mounted')
  }
  return vm
}
```
注意上面的代码中定义了一个updateComponent函数，这个函数执行的时候内部会调用`vm._update(vm._render(), hyddrating)`方法，其中`vm._render`方法会返回一个新的vnode，然后传入vm._update方法后，就用这个新的vnode和老的vnode进行diff，最后完成dom的更新工作。那么updateComponent都是在什么时候去进行调用呢？
```
vm._watcher = new Watcher(vm, updateComponent, noop)
```
实例化一个watcher，在求值的过程中this.value = this.lazy ? undefined : this.get()，会调用this.get()方法，因此在实例化的过程当中Dep.target会被设为这个watcher，通过调用vm._render()方法生成新的Vnode并进行diff的过程中完成了模板当中变量依赖收集工作。即这个watcher被添加到了在模板当中所绑定变量的依赖当中。一旦model中的响应式的数据发生了变化，这些响应式的数据所维护的dep数组便会调用dep.notify()方法完成所有依赖遍历执行的工作，这里面就包括了视图的更新即updateComponent方法，它是在mountComponent中的定义的。

## vue-router原理
推荐文章[vue-router 实现分析](https://cnodejs.org/topic/58d680c903d476b42d34c72b),[vue-router源码分析-整体流程](https://github.com/DDFE/DDFE-blog/issues/9)

![vue-router逻辑流程](vue-router.png)

通过vue的插件机制，在vue.use(VueRouter)时，调用了vueRouter组件中的install方法，install方法中通过vue的mixin，在beforeCreate时执行init方法，init方法中通过监听hashChange事件来监听路由变化。
当监听到hashChange时，执行 history.transitionTo(...)，在这个过程中，会进行地址匹配，得到一个对应当前地址的 route，然后将其设置到对应的 vm._route 上。
在beforeCreate中还采用与 Vue 本身数据相同的“数据劫持”方式，对 vm._route 的赋值会被 Vue 拦截到，并且触发 Vue 组件的更新渲染流程。
```
Vue.util.defineReactive(this, '_route', this._router.history.current)
```
视图更新进一步调用到router-view组件的 render() 方法。主要逻辑是从根组件中取出当前的路由对象（parent.$route），然后取得该路由下对应的组件，然后交由该组件进行渲染。

## vuex原理
推荐文章[Vuex框架原理与源码分析](https://tech.meituan.com/vuex-code-analysis.html)，[Vuex 2.0 源码分析](https://github.com/DDFE/DDFE-blog/issues/8)
管理页面数据，提供统一操作处理
action-mutation-stateChange
Vue组件接收交互行为，调用dispatch方法触发action相关处理，若页面状态需要改变，则调用commit方法提交mutation修改state，通过getters获取到state新值，重新渲染Vue Components，界面随之更新。
![Vuex框架原理与源码分析](vuex.png)

- install方法给Vue的实例注入一个 $store 的属性。
- Vuex 的初始化的核心，其中，installModule 方法是把我们通过 options 传入的各种属性模块注册和安装；resetStoreVM 方法是初始化 store._vm，观测 state 和 getters 的变化；最后是应用传入的插件。
- 严格模式做的事情很简单，监测 `store._vm.state` 的变化，看看 state 的变化是否通过执行 mutation 的回调函数改变，如果是外部直接修改 state，那么 store._committing 的值为 false，这样就抛出一条错误。

## webpack
推荐文章[深入浅出 Webpack](http://webpack.wuhaolin.cn/)

一些重点概念
- Entry：入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。
- Module：模块，在 Webpack 里一切皆模块，一个模块对应着一个文件。Webpack 会从配置的 Entry 开始递归找出所有依赖的模块。
- Chunk：代码块，一个 Chunk 由多个模块组合而成，用于代码合并与分割。
- Loader：模块转换器，用于把模块原内容按照需求转换成新内容。
- Plugin：扩展插件，在 Webpack 构建流程中的特定时机注入扩展逻辑来改变构建结果或做你想要的事情。
- Output：输出结果，在 Webpack 经过一系列处理并得出最终想要的代码后输出结果。

通常你可用如下经验去判断如何配置 Webpack：

- 想让源文件加入到构建流程中去被 Webpack 控制，配置 entry。
- 想自定义输出文件的位置和名称，配置 output。
- 想自定义寻找依赖模块时的策略，配置 resolve。
- 想自定义解析和转换文件的策略，配置 module，通常是配置 module.rules 里的 Loader。
- 其它的大部分需求可能要通过 Plugin 去实现，配置 plugin。

## vue-cli

## electron-vue

