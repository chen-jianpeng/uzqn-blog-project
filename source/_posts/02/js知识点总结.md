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
- `prototype` `__proto__` `constructor` `原型` `原型链`

- 创建对象
  - 工厂模式
    弊端：无法解决对象识别问题（怎样知道一个对象的类型）
    ```
    function createPerson(name, age, job){
      var o = {}
      o.name = name
      o.age = age
      o.job = job
      o.sayName = function(){
        alert(this.name)
      }
      return o
    }
    var person1 = createPerson('xixi', 29, '工程师') 
    var person2 = createPerson('lili', 29, '搬砖') 
    ```
  - 构造函数模式
    优点：不用显式创建对象，属性赋值给this对象，没有return语句。
    constructor指向Person
    弊端：每个方法要在每个实例上重新创建（sayName方法）
    ```
    function Person(name, age, job){
      this.name = name
      this.age = age
      this.job = job
      this.sayName = function(){
        alert(this.name)
      }
    }
    var person1 = new Person('xixi', 29, '工程师') 
    var person2 = new Person('lili', 29, '搬砖') 
    ```
  - 原型模式
  - 组合使用构造函数模式和原型对象模式
