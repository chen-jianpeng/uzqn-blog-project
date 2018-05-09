---
title: es6学习
date: 2018-05-09 11:14:26
tags: js es6
header-img: "head.jpg"
---

以下内容来自于[ECMAScript 6 入门](http://es6.ruanyifeng.com)

## let 和 const 命令
### let：
只在let命令所在的代码块内有效，不存在变量提升，暂时性死区，不存在变量提升。
### const 
一旦声明，常量的值就不能改变。并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。

### 块级作用域
- 允许在块级作用域内声明函数。
- 函数声明类似于var，即会提升到全局作用域或函数作用域的头部。
- 同时，函数声明还会提升到所在的块级作用域的头部。

### es6六种声明变量的方式
var、function、let、const、import、class。

### 顶层对象（window、global）的属性
var命令和function命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。
### global 对象

## 变量的解构赋值

### 数组的解构赋值
```
let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2
let [x = y, y = 1] = [];     // ReferenceError: y is not defined
```

### 对象的解构赋值
默认值生效的条件是，对象的属性值严格等于undefined。
```
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

// 下面foo用于匹配，baz是变量声明
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let x;
{x} = {x: 1};
// SyntaxError: syntax error (将{x}理解成代码块了)
({x} = {x: 1});
x // 1
```

### 字符串的解构赋值
```
const [a, b, c, d, e] = 'hello';
```
### 数值和布尔值的解构赋值
```
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```

### 函数参数的解构赋值
```
function add([x, y]){
  return x + y;
}

add([1, 2]); // 3
```

### 圆括号问题

### 用途
1. 交换变量的值
2. 从函数返回多个值
3. 函数参数的定义
4. 提取 JSON 数据
5. 函数参数的默认值
6. 遍历 Map 结构
7. 输入模块的指定方法

## 字符串的扩展

###  4 个字节的字符的处理
为了处理处理 4 个字节的字符而诞生的方法
- 字符的 Unicode 表示法
- codePointAt()
- String.fromCodePoint()
- 字符串的遍历器接口
- at()
- normalize()

### 是否包含
确定一个字符串是否包含在另一个字符串中：includes(), startsWith(), endsWith()

### 重复
将原字符串重复n次：repeat(n)

### 补全
padStart()，padEnd()

### matchAll
返回一个正则表达式在当前字符串的所有匹配：matchAll()

### 模板字符串
模板字符串的限制：模板字符串默认会将字符串转义，导致无法嵌入其他语言。
对于简单字符串，使用反引号会稍慢一点。

### raw
返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串：String.raw()

## 正则表达式的扩展

## 数值的扩展
二进制和八进制表示法
Number.isFinite(), Number.isNaN()
Number.parseInt(), Number.parseFloat()
Number.isInteger()
Number.EPSILON
安全整数和 Number.isSafeInteger()
Math 对象的扩展
指数运算符

## 函数的扩展

## 数组的扩展

## 对象的扩展