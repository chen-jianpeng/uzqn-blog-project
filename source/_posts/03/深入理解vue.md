---
title: 深入理解vue
date: 2018-03-24 11:05:17
tags: vue
header-img: "head.jpg"
---

## vue与juqery的区别 
通过TODO List实现的例子总结得出
1.数据视图分离，解耦（开放封闭原则：对扩展开放，对修改封闭）。
2.数据驱动视图，只关心数据变化，dom操作被封装。

## MVC
- View 传送指令到 Controller
- Controller 完成业务逻辑后，要求 Model 改变状态
- Model 将新的数据发送到 View，用户得到反馈

View了解Controller，Controller了解Model，而View能够直接访问Model。
例如用户点击了一个按钮（操作了视图），然后controller进行业务逻辑操作，最后将操作结果发送给视图。
!['mvc'](mvc.png)

## MVVM
- M 数据，对应于vue中的data
- V 视图，对应于DOM
- VM 视图模型，对应于vue实例

视图通过事件绑定，经过视图模型操作数据，数据通过数据绑定，经过视图模型修改视图
!['vue'](vue.png)

## vue构造函数
整体流程如下：
1、Vue.prototype 下的属性和方法的挂载主要是在src/core/instance目录中的代码处理的。
2、Vue下的静态属性和方法的挂载主要是在src/core/global-api目录下的代码处理的。
3、web-runtime.js主要是添加web平台特有的配置、组件和指令，web-runtime-with-compiler.js给Vue的$mount方法添加compiler编译器，支持template。
具体来看如下：
定义 Vue 构造函数，然后以Vue构造函数为参数，调用了五个方法，最后导出 Vue。这些方法的作用，就是在 Vue 的原型 prototype 上挂载方法或属性。
```
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}

// initMixin(Vue)    src/core/instance/init.js 
Vue.prototype._init = function (options?: Object) {}

// stateMixin(Vue)    src/core/instance/state.js 
Vue.prototype.$data
Vue.prototype.$set = set
Vue.prototype.$delete = del
Vue.prototype.$watch = function(){}

// renderMixin(Vue)    src/core/instance/render.js 
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

// eventsMixin(Vue)    src/core/instance/events.js 
Vue.prototype.$on = function (event: string, fn: Function): Component {}
Vue.prototype.$once = function (event: string, fn: Function): Component {}
Vue.prototype.$off = function (event?: string, fn?: Function): Component {}
Vue.prototype.$emit = function (event: string): Component {}

// lifecycleMixin(Vue)    src/core/instance/lifecycle.js 
Vue.prototype._mount = function(){}
Vue.prototype._update = function (vnode: VNode, hydrating?: boolean) {}
Vue.prototype._updateFromParent = function(){}
Vue.prototype.$forceUpdate = function () {}
Vue.prototype.$destroy = function () {}
```

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

缓存来自web-runtime.js文件的$mount函数，然后覆盖覆盖了Vue.prototype.$mount。在Vue上挂载compile，compileToFunctions函数的作用，就是将模板template编译为render函数。


## 三要素：
- 响应式：监听数据变化。
- 模板引擎：解析模板指令。
- 渲染：模板结合modal如何生成DOM，当产生变化时更新DOM。

### 响应式
- 修改了data属性之后，vue立刻监听到。
- data被代理到VM上（data中的属性变成vue中的属性，可以通过this访问）。
- 核心实现是通过`Object.defineProperty`API。
  通过在get、set方法中实现对数据的监听。

```
var vm = {}
var data = {
  name: 'HellowWord',
  age: 25
}
// 通过遍历data，将data代理到vm上
for(let key in data){
  Object.defineProperty(vm, key, {
    get:function(){
      console.log('get', key) //监听获取值
      return data[key]
    },
    set:function(newName){
      console.log('set', key) //监听设置值
      data[key] = newName
    }
  })
}
```

### 模板引擎
模板->render函数->DOM
vue中模板的本质就是嵌入js变量的有逻辑的字符串，通过处理得到一个js函数（这里的js函数就是render函数。这里的处理方法，在以前是一个很麻烦的过程，但vue2.0开始支持预编译，开发环境下写模板，经过编译打包，生产环境下变成所需要的js函数），最终被转为HTML。

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

### 渲染
虚拟DOM的两个函数：
- render函数创建虚拟节点（nodeList），
- patch函数：用途1：将虚拟节点渲染到一个空的dom元素上，用途2：新旧节点进行对比，将有差异的部分重新渲染到真实dom节点上。

那么我们就知道了，上边的render函数返回的是一个虚拟dom节点。那vue是如何进行渲染的呢？

初次渲染时，执行UpdateComponent方法，执行render函数。render函数的执行会访问属性值，这样就会被响应式的get方法监听到（绑定依赖）。执行updateComponent方法会触发虚拟dom的patch方法，patch方法将虚拟节点渲染成真正的dom，完成初次渲染。

```
vm._update(vnode) {
  const prevVnode = vm._vnode
  vm._vnode = vnode
  if(!prevVnode){
    vm.$el = vm.__patch__(vm.$el, vnode)
  }else {
    vm.$el = vm.__patch__(prevVnode, vnode)
  }
}

function updateComponent(){
  vm._update(vm.render())
}
```

## 绑定依赖
监听get。为什么要监听get？因为data中不是所有属性都被用到，不被用到我们也就不需要关心，这样避免了不必要的重复渲染。

## 总结：vue整个的实现流程
第一步：解析模板成render函数
第二步：响应式开始监听
第三步：首次渲染，显示页面，且绑定依赖
第四部：data属性变化，触发set监听执行updateComponent方法，然后触发rernder函数（render函数再次执行，重新patch）

