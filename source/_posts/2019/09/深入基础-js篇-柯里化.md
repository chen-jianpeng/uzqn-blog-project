---
title: 深入基础-js篇-柯里化 currying
date: 2019-09-15 08:47:26
tags: [面试, javascript]
header-img: "head.jpg"
---

比较多次接受的参数总数与函数定义时的入参数量，当接受参数的数量大于或等于被 Currying 函数的传入参数数量时，就返回计算结果，否则返回一个继续接受参数的函数。

部分求值（Partial Evaluation），是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

[JavaScript 柯里化](https://juejin.im/post/5af13664f265da0ba266efcf)

```js
function currying(fn, ...args) {
  if (args.length >= fn.length) {
    return fn(...args);
  } else {
    return function(args2) {
      return currying(fn, ...args, ...args2);
    };
  }
}
```

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

### 作用

#### 预加载、参数复用

``` js
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

``` js
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

``` js
<div data-name="name" onClick="handleOnClick" />

<div onClick="handleOnClick.bind(null, data)" />

<div onClick="() => handleOnClick(data)" />

<div onClick="currying(handleOnClick, data)" />
```
