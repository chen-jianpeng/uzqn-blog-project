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