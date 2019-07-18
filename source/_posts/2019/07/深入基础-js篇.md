---
title: 深入基础-js篇
date: 2019-07-17 08:47:26
tags: [面试, javascript]
header-img: "head.jpg"
---

## 数据类型

### 基本类型

### typeof

### instanceof

测试构造函数的 prototype 属性是否出现在对象的原型链中的任何位置

```js
function instanceof(left, right) {
  // 获得类型的原型
  let prototype = right.prototype
  // 获得对象的原型
  left = left.__proto__
  // 判断对象的类型是否等于类型的原型
  while (true) {
    if (left === null)
      return false
    if (prototype === left)
      return true
    left = left.__proto__
  }
}
```

### 类型转换

## 函数

### throttle 节流  

指定时间间隔内只会执行一次任务

每隔一段时间去执行一次原本需要无时不刻地在执行的函数

常用的场景就是滚动

```
function throttle(fn, interval = 300) {
    let canRun = true;
    return function () {
        if (!canRun) return;
        canRun = false;
        setTimeout(() => {
            fn.apply(this, arguments);
            canRun = true;
        }, interval);
    };
}
```

### debounce 函数防抖

任务频繁触发的情况下，只有任务触发的间隔超过指定间隔的时候，任务才会执行

例如输入查重检测，只有在一段时间内没有再次输入才会去请求后台。

```
function debounce(fn, interval = 300) {
    let timeout = null;
    return function () {
        clearTimeout(timeout);
        timeout = setTimeout(() => {
            fn.apply(this, arguments);
        }, interval);
    };
}
```

## call, apply, bind

改变 this 指向。（以下示例未考虑参数的校验）

不适用 es6 的情况下，可以通过 eval 将数组转化成参数序列。

### call

call() 接受一个参数列表,并执行函数。

```js
Function.prototype.myCall = function(context = window, ...args) {
  context.__fn__ = this;

  const result = context.__fn__(...args);

  delete context.__fn__;

  return result;
};
```

### apply

apply() 接受一个数组,并执行函数。

```js
Function.prototype.myApply = function(context = window, args = []) {
  context.__fn__ = this;

  const result = context.__fn__(...args);

  delete context.__fn__;

  return result;
};
```

### bind

`bind()` 会创建一个新函数。当这个新函数被调用时，`bind()` 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。

`bind`函数以下的特点需要注意：

- `bind`传入一部分参数。
- 当`bind`返回的函数作为构造函数的时候，`bind`指定的 this 值会失效，但传入的参数依然生效
- 我们直接修改 fBound.prototype 的时候，也会直接修改绑定函数的 prototype。这个时候，我们可以通过一个空函数来进行中转。

```js
Function.prototype.myBind = function(context, ...bindArgs) {
  if (typeof this !== "function") {
    throw new Error(
      "Function.prototype.bind - what is trying to be bound is not callable"
    );
  }

  var self = this;

  var fNOP = function() {};
  fNOP.prototype = this.prototype;

  var fBound = function(...args) {
    // 当作为构造函数时，this 指向实例，此时结果为 true，
    // 将绑定函数的 this 指向该实例，可以让实例获得来自绑定函数的值
    // 当作为普通函数时，this 指向 window，此时结果为 false，
    // 将绑定函数的 this 指向 context
    return self.apply(
      this instanceof fNOP ? this : context,
      bindArgs.concat(args)
    );
  };

  fBound.prototype = new fNOP();

  return fBound;
};
```

## new

new Foo() 之后发生了什么

1. 一个继承自 Foo.prototype 的新对象被创建。
2. 使用指定的参数调用构造函数 Foo，并将 this 绑定到新创建的对象。
3. 由构造函数返回的对象就是 new 表达式的结果。如果构造函数没有显式返回一个对象，则使用步骤 1 创建的对象。

```
function create() {
    // 创建一个空的对象
    let obj = new Object()
    // 获得构造函数
    let Con = [].shift.call(arguments)
    // 链接到原型
    obj.__proto__ = Con.prototype
    // 绑定 this，执行构造函数
    let result = Con.apply(obj, arguments)
    // 确保 new 出来的是个对象
    return typeof result === 'object' ? result : obj
}
```

## this

- 全局环境中
- 函数（运行环境内）调用
  - 简单的函数调用
  - 严格模式函数调用（指向函数内环境 undefind）
  - 箭头函数 （设置为他被创建时的环境）
  - 使用 call、apply、bind 调用时
  - 作为对象的方法
    - 取决于最近的对象
    - 原型链中
    - setter、getter
  - 在构造函数中
  - 在 dom 监听事件中（target）

## 数组

### 遍历

#### for

#### for-in

#### for-of

##### Symbol.iterator

#### forEach

#### map

#### filter

#### every

#### some

#### reduce

### 操作数组

#### shift

删除第一个元素，并返回该元素的值

## 原型

### 示意图

## 执行上下文

执行上下文：

1. 全局 EC 压入 ECS。
2. 执行某一函数，就为其创建一个 EC，并压入 ECS 栈顶。
3. 如果该函数内再次执行了内部的函数，就重复第二步。
4. 函数执行完毕，该函数的 EC 就会从 ECS 中移除,并且 VO 随之销毁。
5. 所有函数执行完毕之后，ECS 中只剩下全局 EC，在应用关闭时销毁。

创建 EC 时分为两个阶段
创建阶段：

1. 创建 arguments 对象。
2. EC 的参数添加到 VO 对象中。
3. EC 中的函数添加到 VO 中，值是指向函数在内存中的地址。
4. EC 中的变量添加到 VO 中，值为 undefined。

执行阶段：
执行函数体中的代码，给 VO 中的变量赋值。

### EC : 执行上下文

### ECS : 执行环境栈

### VO ： 变量对象

### AO ： 活动对象

### scope chain ：作用域链

## 闭包

不管是函数还是对象，销毁的前提是没有外部引用，跟调用栈无直接关系。

在简单的解释器实现里，函数里的变量是分配在堆而不是在栈上的。
现代 JS 引擎当然就比较牛逼了，通过逃逸分析是可以知道哪些可以分配在栈上，哪些需要分配在堆上的。

## 深浅拷贝

### 浅拷贝

...

assign

### 深拷贝

JSON.parse(JSON.stringify(object))
会忽略 undefined
会忽略 symbol
会忽略 函数
不能解决循环引用的对象

## 模块化

对于 CommonJS 和 ES6 中的模块化的两者区别是：

- 前者支持动态导入，也就是 require(\${path}/xx.js)，后者目前不支持，但是已有提案

- 前者是同步导入，因为用于服务端，文件都在本地，同步导入即使卡住主线程影响也不大。而后者是异步导入，因为用于浏览器，需要下载文件，如果也采用同步导入会对渲染有很大影响

- 前者在导出时都是值拷贝，就算导出的值变了，导入的值也不会改变，所以如果想更新值，必须重新导入一次。但是后者采用实时绑定的方式，导入导出的值都指向同一个内存地址，所以导入值会跟随导出值变化

- 后者会编译成 require/exports 来执行的

### es6

### CommonJS

```
// a.js
module.exports = {
    a: 1
}
// or
exports.a = 1

// b.js
var module = require('./a.js')
module.a // -> log 1
```

### AMD

```
define(['./a', './b'], function(a, b) {
    a.do()
    b.do()
})
define(function(require, exports, module) {
    var a = require('./a')
    a.doSomething()
    var b = require('./b')
    b.doSomething()
})

```

## 继承

子类的原型设置为父类的原型，同时注意将 constructor 重新指向子构造函数。

```
function Super () {
	this.
}
```

### prototype

### constructor

返回创建实例对象的构造函数的引用

创建和初始化 class 创建的对象的特殊方法。

### **proto**

其实这个属性指向了 [[prototype]]，但是 [[prototype]] 是内部属性，我们并不能访问到，所以使用 \_\_proto\_\_ 来访问。

\_\_proto\_\_ 将对象连接起来组成了原型链。

```
function Foo () {}
var a = new Foo()

a.constructor
// Foo () {}

a__prop__
// {
//   constructor： Foo () {}，
//   __prop__: {
//      constructor: Object () {}
//      ……
//   }
// }
```

### [[prototype]]

## promise

```
  // 判断变量否为function
  const isFunction = variable => typeof variable === 'function'
  // 定义Promise的三种状态常量
  const PENDING = 'PENDING'
  const FULFILLED = 'FULFILLED'
  const REJECTED = 'REJECTED'

  class MyPromise {
    constructor (handle) {
      if (!isFunction(handle)) {
        throw new Error('MyPromise must accept a function as a parameter')
      }
      // 添加状态
      this._status = PENDING
      // 添加状态
      this._value = undefined
      // 添加成功回调函数队列
      this._fulfilledQueues = []
      // 添加失败回调函数队列
      this._rejectedQueues = []
      // 执行handle
      try {
        handle(this._resolve.bind(this), this._reject.bind(this))
      } catch (err) {
        this._reject(err)
      }
    }
    // 添加resovle时执行的函数
    _resolve (val) {
      const run = () => {
        if (this._status !== PENDING) return
        // 依次执行成功队列中的函数，并清空队列
        const runFulfilled = (value) => {
          let cb;
          while (cb = this._fulfilledQueues.shift()) {
            cb(value)
          }
        }
        // 依次执行失败队列中的函数，并清空队列
        const runRejected = (error) => {
          let cb;
          while (cb = this._rejectedQueues.shift()) {
            cb(error)
          }
        }
        /* 如果resolve的参数为Promise对象，则必须等待该Promise对象状态改变后,
          当前Promsie的状态才会改变，且状态取决于参数Promsie对象的状态
        */
        if (val instanceof MyPromise) {
          val.then(value => {
            this._value = value
            this._status = FULFILLED
            runFulfilled(value)
          }, err => {
            this._value = err
            this._status = REJECTED
            runRejected(err)
          })
        } else {
          this._value = val
          this._status = FULFILLED
          runFulfilled(val)
        }
      }
      // 为了支持同步的Promise，这里采用异步调用
      setTimeout(run, 0)
    }
    // 添加reject时执行的函数
    _reject (err) {
      if (this._status !== PENDING) return
      // 依次执行失败队列中的函数，并清空队列
      const run = () => {
        this._status = REJECTED
        this._value = err
        let cb;
        while (cb = this._rejectedQueues.shift()) {
          cb(err)
        }
      }
      // 为了支持同步的Promise，这里采用异步调用
      setTimeout(run, 0)
    }
    // 添加then方法
    then (onFulfilled, onRejected) {
      const { _value, _status } = this
      // 返回一个新的Promise对象
      return new MyPromise((onFulfilledNext, onRejectedNext) => {
        // 封装一个成功时执行的函数
        let fulfilled = value => {
          try {
            if (!isFunction(onFulfilled)) {
              onFulfilledNext(value)
            } else {
              let res =  onFulfilled(value);
              if (res instanceof MyPromise) {
                // 如果当前回调函数返回MyPromise对象，必须等待其状态改变后在执行下一个回调
                res.then(onFulfilledNext, onRejectedNext)
              } else {
                //否则会将返回结果直接作为参数，传入下一个then的回调函数，并立即执行下一个then的回调函数
                onFulfilledNext(res)
              }
            }
          } catch (err) {
            // 如果函数执行出错，新的Promise对象的状态为失败
            onRejectedNext(err)
          }
        }
        // 封装一个失败时执行的函数
        let rejected = error => {
          try {
            if (!isFunction(onRejected)) {
              onRejectedNext(error)
            } else {
                let res = onRejected(error);
                if (res instanceof MyPromise) {
                  // 如果当前回调函数返回MyPromise对象，必须等待其状态改变后在执行下一个回调
                  res.then(onFulfilledNext, onRejectedNext)
                } else {
                  //否则会将返回结果直接作为参数，传入下一个then的回调函数，并立即执行下一个then的回调函数
                  onFulfilledNext(res)
                }
            }
          } catch (err) {
            // 如果函数执行出错，新的Promise对象的状态为失败
            onRejectedNext(err)
          }
        }
        switch (_status) {
          // 当状态为pending时，将then方法回调函数加入执行队列等待执行
          case PENDING:
            this._fulfilledQueues.push(fulfilled)
            this._rejectedQueues.push(rejected)
            break
          // 当状态已经改变时，立即执行对应的回调函数
          case FULFILLED:
            fulfilled(_value)
            break
          case REJECTED:
            rejected(_value)
            break
        }
      })
    }
    // 添加catch方法
    catch (onRejected) {
      return this.then(undefined, onRejected)
    }
    // 添加静态resolve方法
    static resolve (value) {
      // 如果参数是MyPromise实例，直接返回这个实例
      if (value instanceof MyPromise) return value
      return new MyPromise(resolve => resolve(value))
    }
    // 添加静态reject方法
    static reject (value) {
      return new MyPromise((resolve ,reject) => reject(value))
    }
    // 添加静态all方法
    static all (list) {
      return new MyPromise((resolve, reject) => {
        /**
         * 返回值的集合
         */
        let values = []
        let count = 0
        for (let [i, p] of list.entries()) {
          // 数组参数如果不是MyPromise实例，先调用MyPromise.resolve
          this.resolve(p).then(res => {
            values[i] = res
            count++
            // 所有状态都变成fulfilled时返回的MyPromise状态就变成fulfilled
            if (count === list.length) resolve(values)
          }, err => {
            // 有一个被rejected时返回的MyPromise状态就变成rejected
            reject(err)
          })
        }
      })
    }
    // 添加静态race方法
    static race (list) {
      return new MyPromise((resolve, reject) => {
        for (let p of list) {
          // 只要有一个实例率先改变状态，新的MyPromise的状态就跟着改变
          this.resolve(p).then(res => {
            resolve(res)
          }, err => {
            reject(err)
          })
        }
      })
    }
    finally (cb) {
      return this.then(
        value  => MyPromise.resolve(cb()).then(() => value),
        reason => MyPromise.resolve(cb()).then(() => { throw reason })
      );
    }
  }

```

## generator

```
function* gen(arg){
    yield 2;
    yield arg;
}

let genHandle = gen(3);
for(let i of genHandle){
  console.log(i);   // 依次打印：2，3
}

let genHandle2 = gen(4);
console.log(genHandle2.next()); // { value: 2, done: false }
console.log(genHandle2.next()); // { value: 4, done: false }
console.log(genHandle2.next()); // { value: undefined, done: true }
```

转换成 es5 的代码大致为下面的情况

```
var context = {
    next:0,
    prev:null,
    gen$:null,
    done:false,
    stop:function(){
      this.done = true;
    }
};
Object.defineProperty(context, Symbol.iterator, {
    enumerable: false,
    writable: false,
    configurable: true,
    value: function () {
        var me = this;
        return {
            next: function () {
               var nextValue = me.gen$(me);
                return {
                    value: nextValue,
                    done: me.done
                }
            }
        }
    }
});
var regeneratorRuntime = {
  wrap:function(_gen){
    context.gen$ = _gen;
    return context;
  }
}
function gen(arg) {
    return regeneratorRuntime.wrap(function gen$(_context) {
        while (1) {
          switch (_context.prev = _context.next) {
            case 0:
              _context.next = 2;
              return 2;
            case 2:
              _context.next = 4;
              return arg;
            case 4:
            case "end":
              return _context.stop();
          }
      }
  });
}
var b = gen(3);
for(var c of b){
  console.log(c);   // 结果为：2，3
}
```

### 自动执行

#### 基于 thunk

```
function run(gen){
  var g = gen();

  function next(data){
    var result = g.next(data);
    if (result.done) return result.value;
    result.value(next);
  }

  next();
}

function* g() {
  // ...
}

run(g);
```

#### 基于 promise

```
function run(gen){
  var g = gen();

  function next(data){
    var result = g.next(data);
    if (result.done) return result.value;
    result.value.then(function(data){
      next(data);
    });
  }

  next();
}

function* g() {
  // ...
}

run(g);
```

## async await

### 优势

#### 内置执行器

#### 更好的语义

#### 更广的适用性

#### 返回值是 promise

### 注意

#### 放在 try catch 中

#### 不是继发关系最好同时执行

#### forEach

#### async 函数可以保留运行堆栈

### 原理

将 Generator 函数和自动执行器，包装在一个函数里

```
async function fn(args) {
  // ...
}

// 等同于

function fn(args) {
  return spawn(function* () {
    // ...
  });
}

function spawn(genF) {
  return new Promise(function(resolve, reject) {
    const gen = genF();
    function step(nextF) {
      let next;
      try {
        next = nextF();
      } catch(e) {
        return reject(e);
      }
      if(next.done) {
        return resolve(next.value);
      }
      Promise.resolve(next.value).then(function(v) {
        step(function() { return gen.next(v); });
      }, function(e) {
        step(function() { return gen.throw(e); });
      });
    }

    // 传名调用而非传值调用
    step(function() { return gen.next(undefined); });
  });
}
```

## proxy

## reflect

- 明显属于语言内部的方法（比如 Object.defineProperty），放到 Reflect 对象上

- 让 Object 操作都变成函数行为

- 不管 Proxy 怎么修改默认行为，你总可以在 Reflect 上获取默认行为

- 修改某些 Object 方法的返回结果，让其变得更合理。比如，Object.defineProperty(obj, name, desc)在无法定义属性时，会抛出一个错误，而 Reflect.defineProperty(obj, name, desc)则会返回 false。

## 正则表达式

### 元字符

| 元字符 |                                      作用                                      |
| :----: | :----------------------------------------------------------------------------: |
|   .    |                         匹配任意字符除了换行符和回车符                         |
|   []   |           匹配方括号内的任意字符。比如 [0-9] 就可以用来匹配任意数字            |
|   ^    | ^9，这样使用代表匹配以 9 开头。[`^`9]，这样使用代表不匹配方括号内除了 9 的字符 |
| {1, 2} |                               匹配 1 到 2 位字符                               |
| (yck)  |                            只匹配和 yck 相同字符串                             |
|   \|   |                              匹配 \| 前后任意字符                              |
|   \    |                                      转义                                      |
|   \*   |                       只匹配出现 0 次及以上 \* 前的字符                        |
|   +    |                        只匹配出现 1 次及以上 + 前的字符                        |
|   ?    |                                 ? 之前字符可选                                 |

### 修饰语

| 修饰语 |    作用    |
| :----: | :--------: |
|   i    | 忽略大小写 |
|   g    |  全局搜索  |
|   m    |    多行    |

### 字符简写

| 简写 |         作用         |
| :--: | :------------------: |
|  \w  | 匹配字母数字或下划线 |
|  \W  |      和上面相反      |
|  \s  |   匹配任意的空白符   |
|  \S  |      和上面相反      |
|  \d  |       匹配数字       |
|  \D  |      和上面相反      |
|  \b  | 匹配单词的开始或结束 |
|  \B  |      和上面相反      |

## requestAnimationFrame

执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。

- 回调函数执行次数通常与浏览器屏幕刷新次数相匹配，一般是每秒60次。
- 运行在后台标签页或者隐藏的`<iframe>` 里时，`requestAnimationFrame()`会被暂停调用。
- 由于JavaScript是单线程的，所以定时器的实现是在当前任务队列完成后再执行定时器的回调的，假如当前队列任务执行时间大于定时器设置的延迟时间，那么定时器就不是那么可靠了。
- setTimeout的定时器值推荐最小使用16.7ms（16.7 = 1000 / 60, 即每秒60帧）。
- CSS3 transition或animation动画也是走的跟你一样的绘制原理。（CSS3动画在Tab切换回来的时候，动画表现并不暂停；Tab切换之后，计算渲染绘制都停止，Tab切换回来时似乎通过内置JS计算了动画位置实现重绘，造成动画不暂停的感觉）。

## 柯里化 currying

比较多次接受的参数总数与函数定义时的入参数量，当接受参数的数量大于或等于被 Currying 函数的传入参数数量时，就返回计算结果，否则返回一个继续接受参数的函数。

部分求值（Partial Evaluation），是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

```
function currying (fn, ...args) {
	if(args.length>=fn.length) {
    	return fn(...args)
    } else {
    	return function(args2) {
        	return currying(fn, ...args, ...args2)
        }
    }
}
```

```
function currying(fn, isEnd=false, ...args) {
  if (isEnd) {
    return fn(...args)
  } else {
    return function(...args2) {
      return currying(fn, args2.length===0, ...args, ...args2)
    }
  }
}

function add (...args){
  return args.reduce((acc,cur)=>{return acc+cur},0)
}

var sum = currying(add)
sum(1)()
sum(1)(2)(3)(4)()
sum(1)(2)(3)(4)(5)(6)()
```

### 作用

#### 预加载、参数复用

```
match(/\s+/g, "hello world");
// [ ' ' ]

match(/\s+/g)("hello world");
// [ ' ' ]

var hasSpaces = match(/\s+/g);
// function(x) { return x.match(/\s+/g) }

hasSpaces("hello world");
hasSpaces("hi world");
```

#### 提前返回

```
var addEvent = function(el, type, fn, capture) {
    if (window.addEventListener) {
        el.addEventListener(type, function(e) {
            fn.call(el, e);
        }, capture);
    } else if (window.attachEvent) {
        el.attachEvent("on" + type, function(e) {
            fn.call(el, e);
        });
    }
};
```

#### 延迟执行

```
<div data-name="name" onClick="handleOnClick" />

<div onClick="handleOnClick.bind(null, data)" />

<div onClick="() => handleOnClick(data)" />

<div onClick="currying( handleOnClick, data)" />
```

## 数据持久化

### session

### cookie

### localstorage

### sessionstorage

参考链接

[javascript 深入系列](https://github.com/mqyqingfeng/Blog/issues?q=is%3Aissue+is%3Aopen+label%3A%E6%B7%B1%E5%85%A5%E7%B3%BB%E5%88%97)
