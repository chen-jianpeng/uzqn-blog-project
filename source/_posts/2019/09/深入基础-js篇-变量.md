---
title: 深入基础-js篇-变量
date: 2019-09-15 08:47:26
tags: [面试, javascript]
header-img: "head.jpg"
---

## 数据定义 const、let

let 与 var 的区别：

- 重复声明：let 会报错
- 暂存死区：var 存在变量提升，而 let 编译时才初始化（let 声明的变量直到它们的定义被执行时才初始化）
- 块级作用域：let 只作用于其声明的块中
- 全局函数无法通过 this 使用 let 定义的全局变量。

```js
let test = 1;
function fn() {
  console.log(this.test);
}
fn(); // undefined
```

## 数据类型

值类型：多个分店，独立经营。String、Number、Boolean、Undefined、Null、Symbol。

引用类型： 一家店，多个钥匙，共同经营。Object、Array、Math。

> null 与 undefined 的区别

null 表示"没有对象"，即该处不应该有值。

undefined 表示"缺少值"，就是此处应该有一个值，但是还没有定义。

> 值类型比较的是值，引用类型比较是否是同一个引用

```js
var a = {};
var b = {};
a === b; // false,a与b指向两个对象
```

> Symbol 的用途：

1. 防止属性名称冲突
2. 消除魔法字符串。
3. node 中摸块执行后得到的是同一个实例。
4. 私有属性：reflect.ownKeys() 方法能够获取对象上所有键的列表，包括字符串和 symbol。通过 proxy 使得 Reflect.ownKeys()无法获取 symbol，从而创建了对象的私有属性。

Object.getOwnPropertySymbols 获取对象的 symbol 属性

symbol 在以下情况下会被忽略：

1. for...in 迭代中不可枚举。
2. JSON.strIngify()时以 symbol 值作为键的属性会被完全忽略。

```js
let proxy;

{
  const favBook = Symbol("fav book");

  const obj = {
    name: "Thomas Hunter II",
    age: 32,
    _favColor: "blue",
    [favBook]: "Metro 2033",
    [Symbol("visible")]: "foo"
  };

  const handler = {
    ownKeys: target => {
      const reportedKeys = [];
      const actualKeys = Reflect.ownKeys(target);

      for (const key of actualKeys) {
        if (key === favBook || key === "_favColor") {
          continue;
        }
        reportedKeys.push(key);
      }

      return reportedKeys;
    }
  };

  proxy = new Proxy(obj, handler);
}

console.log(Object.keys(proxy)); // [ 'name', 'age' ]
console.log(Reflect.ownKeys(proxy)); // [ 'name', 'age', Symbol(visible) ]
console.log(Object.getOwnPropertyNames(proxy)); // [ 'name', 'age' ]
console.log(Object.getOwnPropertySymbols(proxy)); // [Symbol(visible)]
console.log(proxy._favColor); // 'blue'
```

## 数据类型判断

### typeof

1. undefined、boolean、number、string、symbol、function。返回基本类型，null，function 除外。
2. typeof null // object
3. （使用 New 操作符）除 Function 外的所有构造函数的类型都是 'object'

### instanceof

判断一个实例是否属于某个构造函数。测试构造函数的 prototype 属性是否出现在对象的原型链中的任何位置。

只能用来判断两个对象是否属于实例关系， 而不能判断一个对象实例具体属于哪种类型。

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

### constructor

不稳定，constructor 容易被修改。无法识别 null、undefined

```js
new Number().constructor === Number;
```

### toString

```js
Object.prototype.toString.call(1) === "[object Number]";
```

## 类型转换

### 转换规则

#### toNumber

| 输入类型  |            结果            |
| :-------: | :------------------------: |
| undefined |            NaN             |
|   null    |             0              |
|  boolean  |           0 或 1           |
|  object   | valueOf()、toString()、NaN |
|  string   |                            |
|  number   |            不变            |

#### toString

| 输入类型  |                   结果                   |
| :-------: | :--------------------------------------: |
| undefined |                    ''                    |
|   null    |                    ''                    |
|  boolean  |             'true'或'false'              |
|  object   | valueOf()、toString()、'[object Object]' |
|  string   |                   不变                   |
|  number   |                按进制转换                |

### 隐式转换

1. 加号运算符

2. ==

## 变量在内存中如何存储

[js 对象数据结构底层实现原理](https://juejin.im/post/5cde63b3f265da1bd30527a5)

![变量在内存中如何存储](变量存储.jpg)

```js
var a = {};
var b = {};
a === b; // false,a与b指向两个对象
```
