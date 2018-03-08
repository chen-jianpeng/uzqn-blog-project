---
title: ”小红书“读书笔记
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
- `instanceof`

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
    优点：解决了上边两个的问题
    弊端：省略了参数初始化，导致所有实例默认情况下取值相同。
    其共享性，对于包含引用类型值的属性来说，问题就比较突出了。
    ```
    function Person(){

    }

    Person.prototype = {
      constructor:Person,
      name:"xixi",
      age: 29,
      job: '搬砖'，
      friends: ['haha','hehe'],
      sayName: function(){
        alert(this.name)
      }
    }
    var person1 = new Person()
    var person2 = new Person()
    person1.friends.push('lele')
    alert(person1.friends) //'haha,hehe,lele'
    alert(person2.friends) //'haha,hehe,lele'
    alert(person1.friends===person2.friends) //true
    ```
  - 组合使用构造函数模式和原型对象模式
    构造函数用于定义实例属性，原型模式用于定义方法和共享的属性。
    ```
    function Person(name,age,job){
      this.name = name
      this.age = age
      this.job = job
      this.friends = ['haha','hehe']
    }
    Person.prototype = {
      sayName: function(){
        alert(this.name)
      }
    }
    var person1 = new Person('lili',20,'搬砖')
    var person2 = new Person('lvlv'，21，'搬石头')
    person1.friends.push('lele')
    alert(person1.friends) //'haha,hehe,lele'
    alert(person2.friends) //'haha,hehe,lele'
    alert(person1.friends===person2.friends) //false
    alert(person1.sayName===person2.sayName) //true

    alert(Person.prototype.isPrototypeOf(person1)) //true
    alert(person1.hasOwnProperty("name")) //false
    alert("name" in person1) //true
    alert(Object.keys(person1)) //"name,age,job,friends,sayName"

    //对原型对象所做的任何修改，立即从实例上反应出来
    var friend = new Person()
    Person.prototype.sayHi = function(){
      alert('Hi')
    }
    friend.sayHi() //'Hi'
    ```
  - 寄生构造函数模式
    ```
    function SpecialArray(){
      var values = []
      values.push.apply(values,arguments)
      values.toPipedString = function (){
        return this.join("|")
      }
      return values
    }
    var colors = new SpecialArray("red","blue","green");
    alert(colors.toPipedString()) // "red|blue|green"
    ```

## 继承

- 原型链
让一个类型的原型对象等于另一个类型的实例。
问题：包含引用类型值的原型，这个引用类型会被共享。不能向超类型（superType）的构造函数中传递参数。
```
function SuperType(){
  this.property = true
}

SuperType.prototype.getSuperType = function(){
  return this.property
}

function SubType(){
  this.subProperty = false
}

//继承了superType
SubType.prototype = new SuperType();

SubType.prototype.getSubType = function(){
  return this.subProperty
}

var instance = new SubType()
alert(instance.getSuperType()) // true

```

- 借用构造函数
为解决上边的问题，我们借助构造函数。
问题：函数不能复用。超类型的原型中定义的方法，对于子类型是不可见的。
```
function SuperType(name){
  this.name = name 
  this.colors = ["red","blue","yellow"]
}

function SubType(name){
  //继承了SuperType
  SuperType.call(this, name)
}

var instance1 = new SubType('lili')
instance1.color.push("black")
alert(instance1.name) // lili
alert(instance1.colors) //"red,blue,yellow,black"

var instance2 = new SubType('haha')
alert(instance2.name) // haha
alert(instance2.colors) //"red,blue,yellow"
```

- 组合继承
利用原型链对原型属性和方法进行继承，通过构造函数对实例属性进行继承。
第一次调用SuperType时，SubType.prototype中有color属性，并且是共享的。第二次调用SuperType时，SubType本身有color属性，覆盖了SubType.prototype中的color属性，并且SubType本身有color属性在实例之后是独立的。
```
function SuperType(name){
  this.name = name
  this.color = ["red","blue","yellow"]
}

SuperType.prototype.sayName = function(){
  alert(this.name)
}

function SubType(name, age){
  SuperType.call(this,name)//第二次调用SuperType
  this.age = age
}

SubType.prototype = new SuperType()//第一次调用SuperType
SubType.prototype.constructor = SubType
SubType.prototype.sayAge = function (){
  alert(this.age)
}

var instance1 = new SubType('lili',25)
instance1.color.push("black")
alert(instance1.color) //"red,blue,yellow,black"
instance1.sayName() //"lili"
instance1.sayAge() //25

var instance2 = new SubType('hehe',20)
alert(instance2.color) //"red,blue,yellow"
instance2.sayName() //"hehe"
instance2.sayAge() //20
```

- 原型式继承
问题：引用类型值的属性共享
```
function object(o){
  function F (){

  }
  F.prototype = o
  return new F()
}

var person = {
  name:"hehe",
  friends:["lili","haha"]
}

var anotherPerson = object(person)
anotherPerson.name = "pipi"
anotherPerson.friends.push("papa")

var yetAnotherPerson = object(person)
yetAnotherPerson.name = "kiki"
yetAnotherPerson.friends.push("nini")

alert(person.friends)//"lili,haha,papa,nini"
```

- 寄生式继承
```
function object(o){
  function F (){

  }
  F.prototype = o
  return new F()
}

function createAnother(original){
  var clone = object(original)
  clone.sayHi = function() {
    alert('Hi)
  }
  return clone
}

var person = {
  name: 'lili',
  friends: ["mimi","titi"]
}
var anotherPerson = createAnother(person)
anotherPerson.sayHi() //Hi
```

- 寄生组合式继承
为解决组合式继承会调用两次超类型构造函数的问题，所以诞生了寄生组合式继承。
所谓寄生组合式继承，就是通过构造函数来继承属性，通过原型链的混成形式来继承方法。
  - 只调用一次SuperType,避免因此在SubType上面创建不必要的、多余的属性。
  - 原型链保持不变，所以可以正常使用incetanceof和isPrototypeOf。
```
function object(o){
  function F (){

  }
  F.prototype = o
  return new F()
}

function inheritPrototype(subType,superType){
  var prototype = object(superType.prototype)
  prototype.constructor = subType
  subType.prototype = prototype
}

function SuperType(name){
  this.name = name
  this.colors = ["red","yellow","blue"]
}

SuperType.prototype.sayName = function(){
  alert(this.name)
}

function SubType(name,age){
  SuperType.call(this,name)
  this.age = age
}

inheritPrototype (SubType,SuperType)

SubType.prototype.sayAge = function (){
  alert(this.age)
}
```
