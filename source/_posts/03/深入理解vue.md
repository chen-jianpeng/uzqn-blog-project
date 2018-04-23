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

## 源码分析
[Vue源码学习](http://hcysun.me/2017/03/03/Vue%E6%BA%90%E7%A0%81%E5%AD%A6%E4%B9%A0/)

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
Vue.prototype._init = function (options?: Object) {}

// stateMixin(Vue) 数据绑定，$watch方法   src/core/instance/state.js 
Vue.prototype.$data
Vue.prototype.$set = set
Vue.prototype.$delete = del
Vue.prototype.$watch = function(){}

// renderMixin(Vue) 渲染方法，用来生成render function 和 VNode   src/core/instance/render.js 
Vue.prototype.$nextTick = function (fn: Function) {}
Vue.prototype._render = function (): VNode {}
Vue.prototype._s = _toString
Vue.prototype._v = createTextVNode
Vue.prototype._n = toNumber
Vue.prototype._e = createEmptyVNode
Vue.prototype._q = looseEqual
Vue.prototype._i = looseIndexOf
Vue.prototype._m = function(){}
Vue.prototype._o = function(){}
Vue.prototype._f = function resolveFilter (id) {}
Vue.prototype._l = function(){}
Vue.prototype._t = function(){}
Vue.prototype._b = function(){}
Vue.prototype._k = function(){}

// eventsMixin(Vue) 事件方法，比如$on,$off,$emit   src/core/instance/events.js 
Vue.prototype.$on = function (event: string, fn: Function): Component {}
Vue.prototype.$once = function (event: string, fn: Function): Component {}
Vue.prototype.$off = function (event?: string, fn?: Function): Component {}
Vue.prototype.$emit = function (event: string): Component {}

// lifecycleMixin(Vue) 生命周期方法   src/core/instance/lifecycle.js 
Vue.prototype._mount = function(){}
Vue.prototype._update = function (vnode: VNode, hydrating?: boolean) {}
Vue.prototype._updateFromParent = function(){}
Vue.prototype.$forceUpdate = function () {}
Vue.prototype.$destroy = function () {}
```

#### 绑定静态属性和方法
导入上文导出的Vue（已经在原型上挂载了方法和属性），将Vue作为参数传给initGlobalAPI ，最后又在 Vue.prototype上挂载了 $isServer，在Vue上挂载了version属性。
```
initGlobalAPI(Vue)

Object.defineProperty(Vue.prototype, '$isServer', {
  get: isServerRendering
})

Vue.version = '__VERSION__'
```

initGlobalAPI 的作用是在 Vue 构造函数上挂载静态属性和方法，Vue 在经过 initGlobalAPI 之后，会变成这样：
```
// src/core/index.js / src/core/global-api/index.js
Vue.config
Vue.util = util
Vue.set = set
Vue.delete = del
Vue.nextTick = util.nextTick
Vue.options = {
  components: {
      KeepAlive
  },
  directives: {},
  filters: {},
  _base: Vue
}
Vue.use
Vue.mixin
Vue.cid = 0
Vue.extend
Vue.component = function(){}
Vue.directive = function(){}
Vue.filter = function(){}

Vue.prototype.$isServer
Vue.version = '__VERSION__'
```

#### 安装指令和组件
覆盖Vue.config的属性，将其设置为平台特有的一些方法。Vue.options.directives和Vue.options.components安装平台特有的指令和组件。在Vue.prototype上定义`__patch__`和 `$mount`。
```
// 安装平台特定的utils
Vue.config.isUnknownElement = isUnknownElement
Vue.config.isReservedTag = isReservedTag
Vue.config.getTagNamespace = getTagNamespace
Vue.config.mustUseProp = mustUseProp
// 安装平台特定的指令和组件
Vue.options = {
  components: {
    KeepAlive,
    Transition,
    TransitionGroup
  },
  directives: {
    model,
    show
  },
  filters: {},
  _base: Vue
}
Vue.prototype.__patch__
Vue.prototype.$mount
```

#### 覆盖$mount,编译模板函数
缓存来自web-runtime.js文件的$mount函数，然后覆盖覆盖了Vue.prototype.$mount。在Vue上挂载compile，compileToFunctions函数的作用，就是将模板template编译为render函数。

#### 一个vue实例化过程的例子
实例化一个vue对象
```
let v = new Vue({
  el: '#app',
  data: {
    a: 1,
    b: [1, 2, 3]
  }
})
```
首先执行_init(option),使用策略对象合并参数选项
```
Vue.prototype._init = function (options?: Object) {
  const vm: Component = this
  // a uid
  vm._uid = uid++
  // a flag to avoid this being observed
  vm._isVue = true
  // merge options
  if (options && options._isComponent) {
    // optimize internal component instantiation
    // since dynamic options merging is pretty slow, and none of the
    // internal component options needs special treatment.
    initInternalComponent(vm, options)
  } else {
    vm.$options = mergeOptions(
      resolveConstructorOptions(vm.constructor),
      options || {},
      vm
    )
  }
  /* istanbul ignore else */
  if (process.env.NODE_ENV !== 'production') {
    initProxy(vm)
  } else {
    vm._renderProxy = vm
  }

  // expose real self
  vm._self = vm
  initLifecycle(vm)
  initEvents(vm)
  callHook(vm, 'beforeCreate')
  initState(vm)
  callHook(vm, 'created')
  initRender(vm)
}
```
上边给mergeOptions方法传递的参数相当于下边的形式。
```
vm.$options = mergeOptions(
  // Vue.options
  {
    components: {
      KeepAlive,
      Transition,
      TransitionGroup
    },
    directives: {
      model,
      show
    },
    filters: {},
    _base: Vue
  },
  // 调用Vue构造函数时传入的参数选项 options
  {
    el: '#app',
    data: {
      a: 1,
      b: [1, 2, 3]
    }
  },
  // this
  vm
)
```

实例化结果
```
// 在 Vue.prototype._init 中添加的属性 
this._uid = uid++
this._isVue = true
this.$options = {
    components,
    directives,
    filters,
    _base,
    el,
    data: mergedInstanceDataFn()
}
this._renderProxy = this
this._self = this

// 在 initLifecycle 中添加的属性
this.$parent = parent
this.$root = parent ? parent.$root : this

this.$children = []
this.$refs = {}

this._watcher = null
this._inactive = false
this._isMounted = false
this._isDestroyed = false
this._isBeingDestroyed = false

// 在 initEvents     中添加的属性 
this._events = {}
this._updateListeners = function(){}

// 在 initState 中添加的属性
this._watchers = []
    // initData
    this._data

// 在 initRender     中添加的属性     
this.$vnode = null // the placeholder node in parent tree
this._vnode = null // the root of the child tree
this._staticTrees = null
this.$slots
this.$scopedSlots
this._c
this.$createElement
```

初始化工作与Vue实例对象的设计

### 数据响应系统
Vue的数据响应系统包含三个部分：Observer、Dep、Watcher。

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

Vue生命周期当中初始化的最后阶段：将vm实例挂载到dom上，源码在src/core/instance/init.js
```
Vue.prototype._init = function () {
    ...
    vm.$mount(vm.$options.el)  
    ...
}
```
实际上是调用了src/core/instance/lifecycle.js中的mountComponent方法，
mountComponent函数的定义是：
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
当 vm._render 执行的时候，所依赖的变量就会被求值，并被收集为依赖。按照Vue中 watcher.js 的逻辑，当依赖的变量有变化时不仅仅回调函数被执行，实际上还要重新求值，即还要执行一遍。

模板内容如下
```
<div id="app">
  <div>
    <input v-model="title">
    <button v-on:click="add">submit</button>
  </div>
  <div>
    <ul>
      <li v-for="item in list">{{item}}</li>
    </ul>
  </div>
</div>

<script type="text/javascript">
  // data 独立
  var data = {
    title: '',
    list: []
  }
  // 初始化 Vue 实例
  var vm = new Vue({
    el: '#app',
    data: data,
    methods: {
      add: function () {
      this.list.push(this.title)
      this.title = ''
      }
    }
  })
</script>
```

上面的模板将生成如下的render函数，函数中get，set了data，实现双向数据绑定。
``` 
with(this){  // this 就是 vm
  return _c(
    'div',
    {
      attrs:{"id":"app"}
    },
    [
      _c(
        'div',
        [
          _c(
            'input',
            {
              directives:[
                {
                  name:"model",
                  rawName:"v-model",
                  // 这里get title这个值，显示
                  value:(title),
                  expression:"title"
                }
              ],
              domProps:{
                "value":(title)
              },
              on:{
                "input":function($event){
                  if($event.target.composing)return;
                  // 这里set title
                  title=$event.target.value
                }
              }
            }
          ),
          _v(" "),
          _c(
            'button',
            {
              on:{
                "click":add
              }
            },
            [_v("submit")]
            )
        ]
        ),
        _v(" "),
        _c('div',
          [
            _c(
              'ul',
              _l((list),function(item){return _c('li',[_v(_s(item))])})
            )
          ]
          )
    ]
  )
}
```

vm_render 方法最终返回一个 vnode 对象，即虚拟DOM，然后作为 vm_update 的第一个参数传递了过去，我们看一下 vm_update 的逻辑，在 src/core/instance/lifecycle.js 文件中有这么一段代码：
```
if (!prevVnode) {
  // initial render
  vm.$el = vm.__patch__(
    vm.$el, vnode, hydrating, false /* removeOnly */,
    vm.$options._parentElm,
    vm.$options._refElm
  )
} else {
  // updates
  vm.$el = vm.__patch__(prevVnode, vnode)
}
```
如果还没有 prevVnode 说明是首次渲染，直接创建真实DOM。如果已经有了 prevVnode 说明不是首次渲染，那么就调用patch函数，patch函数用新的vnode和老的vnode进行diff，最后完成dom的更新工作。这就是Vue更新DOM的逻辑。

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
[Vue 2.0 的 virtual-dom 实现简析](https://github.com/DDFE/DDFE-blog/issues/18)
[深入 Vue2.x 的虚拟 DOM diff 原理](https://cloud.tencent.com/developer/article/1006029)

## vue-router原理
[vue-router 实现分析](https://cnodejs.org/topic/58d680c903d476b42d34c72b)
[vue-router源码分析-整体流程](https://github.com/DDFE/DDFE-blog/issues/9)

## vuex原理
[Vuex框架原理与源码分析](https://tech.meituan.com/vuex-code-analysis.html)