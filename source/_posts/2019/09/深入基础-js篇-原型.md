---
title: 深入基础-js篇-原型
date: 2019-09-15 08:47:26
tags: [面试, javascript]
header-img: "head.jpg"
---

## js 中原型的规则

- 所有的引用类型（数组、对象、函数），都具有对象特征，即可自由扩展属性；
- 所有的引用类型，都有一个`_proto_` 属性（隐式原型），属性值是一个普通对象；
- 所有函数，都具有一个`prototype`（显示原型），他是一个指针，指向一个对象，这个对象存放着所有实例共享的属性和方法；
- 所有的引用类型（数组、对象、函数），其隐式原型指向其构造函数的显式原型；（`obj._proto_ === Object.prototype`）；
- 当试图得到一个对象的某个属性时，如果这个对象本身没有这个属性，那么会去它的`_proto_`（即它的构造函数的 prototype）中去寻找；

## 原型链

当试图得到一个对象的某个属性时，如果这个对象本身没有这个属性，那么会去它的`_proto_`，还是没有就再找`_prop_._prop_`。

## 原型模式

```js
function Person() {}
Person.prototype.name = "caixukun";
Person.prototype.age = 20;
Person.prototype.sayName = function() {
  console.log(this.name);
};

let person = new Person();
```

### 原型对象 prototype

prototype 就是 person 实例的原型对象。

### 原型对象 prototype 的 constructor

Person.prototype.constructor 指向 Person

### prototype.isPrototypeOf()

判断 person 实例的`_prop_`是否指向构造函数的原型对象 Person.prototype。

只要是原型链中出现过的原型，都可以说是该原型链所派生的实例的原型。

```js
Person.prototype.isPrototypeOf(person); // true
person._prop_ === Person.prototype;
```

### Object.getPrototypeOf()

用于获取对象的*prop*

### Object.hasOwnPrototype()

检测属性来自于实例（返回 true）还是原型（返回 false）。

### in

判断对象是否能够访问给定的属性。

### Object.keys()

返回对象上所有可枚举的实例属性。

### getOwnPrototypeNames()

返回对象上无论是否可枚举的实例属性。

### Object.defineProperty()

```js
// Person.prototype.constructor将被覆盖
Person.prototype = {
  name: "jay zhou",
  age: 40
};

// 这样是的constructor属性变成了可枚举enumberable
Person.prototype.constructor = Person;

// 最后通过defineProperty来设置
Object.defineProperty(Person.prototype, constructor, {
  enumberable: false,
  value: Person
});
```

### 原型的动态性

对原型的修改会从实例上反映出来。因为实例与原型之间通过指针连接，而不是一个副本。

重写原型对象时，切断了现有原型与任何之前已经存在了的对象实例之间的联系，他们引用的仍然是之前的原型。

### 存在的问题

引用类型值的情况下，如果希望实例间的属性相互独立，就会产生问题。

```js
function Person() {}
Person.prototype.friends = ["caixukun", "jay zhou"];

var person1 = new Person();
var person2 = new Person();

person1.friends.push("chenjp");

person1.friends; // 'caixukun,jay zhou, chenjp'
person2.friends; // 'caixukun,jay zhou, chenjp'
```

### 构造函数模式 + 原型模式 （推荐）

通过构造函数模式结合原型模式可以解决上边的问题。

```js
function Person(name) {
  this.name = name;
  this.friends = ["caixukun", "jay zhou"];
}
Person.prototype.sayName = function() {
  console.log(this.name);
};

var person1 = new Person("sam");
var person2 = new Person("lily");

person1.friends.push("chenjp");

person1.friends; // 'caixukun,jay zhou, chenjp'
person2.friends; // 'caixukun,jay zhou'
```

### 动态原型模式

通过在构造函数中检查是否存在，来决定是否初始化原型。

## 继承

### 原型链继承

让子类型的原型等于父类型的实例。`Cat.prototype = new Animal()`
父类型中的引用类型会产生问题。像下面的例子，eat 属性转移到了 Cat.prototype。

```js
function Animal() {
  this.species = "动物";
  this.eat = ["water"];
}
Animal.prototype.saySpecies = function() {
  console.log(this.species);
};

function Cat() {
  this.name = "猫";
}
Cat.prototype = new Animal();
Cat.prototype.say = function() {
  console.log("喵");
};

var cat1 = new Cat();
cat1.eat.push("fish");

var cat2 = new Cat();

cat1.eat; // 'water'、'fish'
cat2.eat; // 'water'、'fish'
```

### 借助构造函数继承

子类型的构造函数中调用超类型的构造函数。`Animal.call(this)`
超类型中原型上定义的方法，子类型中不可见。cat1 无法访问 saySpecies 方法。

```js
function Animal() {
  this.species = "动物";
  this.eat = ["water"];
}
Animal.prototype.saySpecies = function() {
  console.log(this.species);
};

function Cat() {
  Animal.call(this);
  this.name = "猫";
}
Cat.prototype.say = function() {
  console.log("喵");
};

var cat1 = new Cat();
cat1.eat.push("fish");

var cat2 = new Cat();

cat1.eat; // 'water'、'fish'
cat2.eat; // 'water'

cat1.saySpecies(); // is not a function
```

### 原型链继承 + 构造函数继承 (推荐)

原型链实现了对原型属性和方法的继承，构造函数实现了对实例属性的继承。

```js
function Animal() {
  this.species = "动物";
  this.eat = ["water"];
}
Animal.prototype.saySpecies = function() {
  console.log(this.species);
};

function Cat() {
  Animal.call(this);
  this.name = "猫";
}
Cat.prototype = new Animal();
Cat.prototype.constructor = Cat;
Cat.prototype.say = function() {
  console.log("喵");
};

var cat1 = new Cat();
cat1.eat.push("fish");

var cat2 = new Cat();

cat1.eat; // 'water'、'fish'
cat2.eat; // 'water'

cat1.saySpecies(); // is not a function
```

### 原型式继承

仅用于让一个对象与另一个对象保持类似的情况。引用类型会产生问题。

```js
var animal = {
  species: "动物",
  eat: ["water"],
  saySpecies: function() {
    console.log(this.species);
  }
};

var cat1 = Object.create(animal);
cat1.name = "猫1";
cat1.eat.push("fish");

var cat2 = Object.create(animal);
cat2.name = "猫2";

animal.friends = [1, 2, 3, 4];
cat1.friends; // [1, 2, 3, 4]
```

### 寄生式继承

不能复用函数。

```js
function createAnother(original) {
  var clone = Object.create(original);
  clone.sayHi = function() {
    console.log("hi");
  };
  return clone;
}

var animal = {
  species: "动物",
  eat: ["water"],
  saySpecies: function() {
    console.log(this.species);
  }
};

var cat1 = createAnother(animal);
```

## new

new Fn() 之后发生了什么

1. 一个继承自 Foo.prototype 的新对象被创建。
2. 使用指定的参数调用构造函数 Foo，并将 this 绑定到新创建的对象。
3. 由构造函数返回的对象就是 new 表达式的结果。如果构造函数没有显式返回一个对象，则使用步骤 1 创建的对象。

``` js
function create(fn) {
    // 创建一个空的对象
    let obj = new Object()
    // 链接到原型
    obj.__proto__ = fn.prototype
    // 绑定 this，执行构造函数
    let result = fn.apply(obj, arguments)
    // 确保 new 出来的是个对象
    return typeof result === 'object' ? result : obj
}
```

## es6 class

### 实现原理

[ES6类以及继承的实现原理](https://segmentfault.com/a/1190000014798678)

```js
"use strict";

var _createClass = (function() {
  function defineProperties(target, props) {
    for (var i = 0; i < props.length; i++) {
      var descriptor = props[i];
      descriptor.enumerable = descriptor.enumerable || false;
      descriptor.configurable = true;
      if ("value" in descriptor) descriptor.writable = true;
      Object.defineProperty(target, descriptor.key, descriptor);
    }
  }

  return function(Constructor, protoProps, staticProps) {
    if (protoProps) defineProperties(Constructor.prototype, protoProps);
    if (staticProps) defineProperties(Constructor, staticProps);
    return Constructor;
  };
})();

function _classCallCheck(instance, Constructor) {
  if (!(instance instanceof Constructor)) {
    throw new TypeError("Cannot call a class as a function");
  }
}

var Parent = (function() {
  function Parent(name, age) {
    _classCallCheck(this, Parent);

    this.name = name;
    this.age = age;
  }

  _createClass(Parent, [
    {
      key: "speakSomething",
      value: function speakSomething() {
        console.log("I can speek chinese");
      }
    }
  ]);

  return Parent;
})();
```
