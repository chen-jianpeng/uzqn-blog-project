---
title: 深入基础-js篇
date: 2019-09-15 08:47:26
tags: [面试, javascript]
header-img: "head.jpg"
---

## 函数

### throttle 节流  

指定时间间隔内只会执行一次任务

每隔一段时间去执行一次原本需要无时不刻地在执行的函数

常用的场景就是滚动

```js
function throttle(fn, interval = 300) {
  let canRun = true;
  return function() {
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

例如搜索查询，只有在一段时间内没有再次输入才会去请求后台。

```js
function debounce(fn, interval = 300) {
  let timeout = null;
  return function() {
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      fn.apply(this, arguments);
    }, interval);
  };
}
```

## 数组

### 遍历

#### for

#### for-in

应用于数组：循环返回的是数组的下标和数组的属性和原型上的方法和属性

应用于对象：循环返回的是对象的属性名和原型中的方法和属性。

#### for-of

for...of 循环可以使用的范围包括数组、Set 和 Map 结构、某些类似数组的对象（比如 arguments 对象、DOM NodeList 对象）、后文的 Generator 对象，以及字符串。

Object.entries()方法返回一个给定对象自身可枚举属性的键值对数组，其排列与使用 for...in 循环遍历该对象时返回的顺序一致（区别在于 for-in 循环也枚举原型链中的属性）。

#### forEach

#### map

#### filter

#### every

#### some

#### reduce

### 操作数组

#### shift

删除第一个元素，并返回该元素的值

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

## 数据持久化

### session

### cookie

### localstorage

### sessionstorage

参考链接

[javascript 深入系列](https://github.com/mqyqingfeng/Blog/issues?q=is%3Aissue+is%3Aopen+label%3A%E6%B7%B1%E5%85%A5%E7%B3%BB%E5%88%97)
