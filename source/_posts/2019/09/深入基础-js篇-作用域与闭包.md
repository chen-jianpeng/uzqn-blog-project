---
title: 深入基础-js篇-作用域与闭包
date: 2019-09-15 08:47:26
tags: [面试, javascript]
header-img: "head.jpg"
---

## 作用域

[JavaScript 深入之词法作用域和动态作用域](https://github.com/mqyqingfeng/Blog/issues/3)

### 静态作用域和动态作用域

JavaScript 采用的是静态作用域。

静态、动态作用域都是先从自己的函数内部查找，找不到后：
静态作用域：根据书写的位置，在上面一层的代码查找。（定义时）
动态作用域：在调用函数的作用域中查找。（执行时）

```js
var value = 1;

function foo() {
  console.log(value);
}

function bar() {
  var value = 2;
  foo();
}

bar();
```

### 词法作用域

函数的作用域基于函数创建的位置。
函数的执行用到了作用域链，这个作用域链是在函数**定义时**创建的。

```js
var scope = "global scope";
function checkscope() {
  var scope = "local scope";
  function f() {
    return scope;
  }
  return f();
}
checkscope();
```

```js
var scope = "global scope";
function checkscope() {
  var scope = "local scope";
  function f() {
    return scope;
  }
  return f;
}
checkscope()();
```

## 执行上下文

### 变量对象(Variable object，VO)

### 作用域链(Scope chain)

当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级(词法层面上的父级)执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的变量对象构成的链表就叫做作用域链。

### this

this 指的是函数**运行时**所在的环境。

#### 不同情况下 this 的值

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

#### 改变 this 的指向

call 接受一个参数列表。apply 接受一个数组。bind 返回一个函数。

例如改变 test 函数的 this 指向为 newThis
模拟实现思路，newThis 添加一个属性，值为 test 函数，然后在执行完以后删除

##### call

call() 接受一个参数列表,并执行函数。

```js
Function.prototype.myCall = function(context = window, ...args) {
  context.__fn__ = this;

  const result = context.__fn__(...args);

  delete context.__fn__;

  return result;
};
```

##### apply

apply() 接受一个数组,并执行函数。

```js
Function.prototype.myApply = function(context = window, args = []) {
  context.__fn__ = this;

  const result = context.__fn__(...args);

  delete context.__fn__;

  return result;
};
```

##### bind

`bind()` 会创建一个新函数。当这个新函数被调用时，`bind()` 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。

[JavaScript 深入之 bind 的模拟实现](https://github.com/mqyqingfeng/Blog/issues/12)

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

## 闭包

闭包是指那些能够访问自由变量（在函数中使用的，但既不是函数参数也不是函数的局部变量的变量）的函数。

### 作用域链的作用

```js
var scope = "global scope";
function checkscope() {
  var scope = "local scope";
  function f() {
    return scope;
  }
  return f;
}

var foo = checkscope();
foo();
```

当 f 函数执行的时候，checkscope 函数上下文已经被销毁了啊(即从执行上下文栈中被弹出)，怎么还会读取到 checkscope 作用域下的 scope 值呢？
因为 f 执行上下文维护了一个作用域链。

### 老套路

```js
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = function() {
    console.log(i);
  };
}

data[0]();
data[1]();
data[2]();
```

```js
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = (function(i) {
    return function() {
      console.log(i);
    };
  })(i);
}

data[0]();
data[1]();
data[2]();
```

### 闭包的应用

#### 防抖函数 debounce

```js
function debounce(fn, interval = 300) {
  var timer;
  return function(...args) {
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apllay(this, args);
    }, interval);
  };
}
```

#### 节流函数 throttle

```js
function throttle(fn, interval = 300) {
  var canRun = true;
  return function(...args) {
    if (!canrun) return;
    canrun = false;
    setTimeout(() => {
      fn.applay(this, args);
      canrun = true;
    }, interval);
  };
}
```

#### 柯里化 curring

```js
function currying(fn, isEnd = false, ...args) {
  if (isEnd) {
    return fn(...args);
  } else {
    return function(...args2) {
      return currying(fn, args2.length === 0, ...args, ...args2);
    };
  }
}

function add(...args) {
  return args.reduce((acc, cur) => {
    return acc + cur;
  }, 0);
}

var sum = currying(add);
sum(1)();
sum(1)(2)(3)(4)();
sum(1)(2)(3)(4)(5)(6)();
```

## 内存

### 内存泄漏

[JavaScript 中的内存泄漏以及如何处理](https://juejin.im/entry/5a1256a551882531e944692b)

不再用到的内存，没有及时释放，就叫做内存泄漏（memory leak）。

如果内存占用基本平稳，接近水平，就说明不存在内存泄漏。反之，就是内存泄漏了。

- 不必要的全局变量
- 被遗忘的计时器或回调
- 闭包
- 超出 DOM 引用

### 内存溢出

## 模块化

对于 CommonJS 和 ES6 中的模块化的两者区别是：

- 前者支持动态导入，也就是 require(\${path}/xx.js)，后者目前不支持，但是已有提案

- 前者是同步导入，因为用于服务端，文件都在本地，同步导入即使卡住主线程影响也不大。而后者是异步导入，因为用于浏览器，需要下载文件，如果也采用同步导入会对渲染有很大影响

- 前者在导出时都是值拷贝，就算导出的值变了，导入的值也不会改变，所以如果想更新值，必须重新导入一次。但是后者采用实时绑定的方式，导入导出的值都指向同一个内存地址，所以导入值会跟随导出值变化

- 后者会编译成 require/exports 来执行的

### es6

### CommonJS

```js
// a.js
module.exports = {
  a: 1
};
// or
exports.a = 1;

// b.js
var module = require("./a.js");
module.a; // -> log 1
```

### AMD

```js
define(["./a", "./b"], function(a, b) {
  a.do();
  b.do();
});
define(function(require, exports, module) {
  var a = require("./a");
  a.doSomething();
  var b = require("./b");
  b.doSomething();
});
```
