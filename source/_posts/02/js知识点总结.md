---
title: js知识点总结
date: 2018-02-08 13:44:26
tags: js
header-img: "head.jpg"
---

js学习中有些东西，当时看过，过后就忘了，这里还是总结一下，没事就拿出来翻一翻吧。以下内容来自互联网。

## call apply bind的使用

```
// 合并数组
Array.prototype.push.apply(array1, array2); 

// 数组中找到最大值
maxInNumbers = Math.max.apply(Math, numbers);
maxInNumbers = Math.max.call(Math,5, 458 , 120 , -215);

// 验证数据类型
Object.prototype.toString.call(obj) === '[object Array]';

// 伪数组转数组（dom元素，方法的arguments）
Array.prototype.slice.call(document.getElementsByTagName("*"));
Array.prototype.slice.call(arguments);

// 一个例子，打印日志的方法
function log(){
  var args = Array.prototype.slice.call(arguments);
  args.unshift('(app)');
 
  console.log.apply(console, args);
};
```

## arguments，instanceof

## 作用域链

## 数组方法

- `instanceof`
- `push`、`pop`
- `shift`、`unshift`
- `toString`、`valueOf`
- `reverse`、`sort`
- `concat`、`slice`、`splice`
- `indexOf`、`lastIndexOf`
- `every`、`filter`、`forEach`、`map`、`some`
- `reduce`、`reduceRight`

## 正则表达式

- 字面量形式创建、`RegExp`构造函数创建
- `g`全局匹配、`i`忽略大小写、`m`多行匹配
- `exec`、`test`方法

## 函数

- 函数声明与函数表达式

```
a() //1
var a = function(){ alert(2) }
function a () { alert(1) }
a() //2
```

- `arguments` 类数组对象
- `this`引用的是函数执行的环境对象

## String方法

- `chaAt` `charCodeAt`
- `concat`
- `slice` `substr` `substring`
- `indexOf` `lastIndexOf`
- `trim`
- `toLowerCase` `toUpperCase`
- `match` `search` `replace` `split`
- `localeCompare`
- `fromCharCode`

## Math方法

- `min` `max`
- `ceil` `floor` `round`
- `random`
- `abs` `exp` `log` `pow` `sqrt` 
- `cos` `sin` `tan` `acos` `asin` `atan`

## 对象

- `property` `defineProperty`