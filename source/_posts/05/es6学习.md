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
### 二进制和八进制表示法
从 ES5 开始，在严格模式之中，八进制就不再允许使用前缀0表示，ES6 进一步明确，要使用前缀0o表示。

### Number.isFinite(), Number.isNaN()
它们与传统的全局方法isFinite()和isNaN()的区别在于，传统方法先调用Number()将非数值的值转为数值，再进行判断，而这两个新方法只对数值有效，Number.isFinite()对于非数值一律返回false, Number.isNaN()只有对于NaN才返回true，非NaN一律返回false。

### Number.parseInt(), Number.parseFloat()
ES6 将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变。
这样做的目的，是逐步减少全局性方法，使得语言逐步模块化。

### Number.isInteger()
判断一个数值是否为整数

### Number.EPSILON
极小的常量Number.EPSILON。Number.EPSILON === Math.pow(2, -52)

误差范围设为 2 的-50 次方（即Number.EPSILON * Math.pow(2, 2)），即如果两个浮点数的差小于这个值，我们就认为这两个浮点数相等。
```
function withinErrorMargin (left, right) {
  return Math.abs(left - right) < Number.EPSILON * Math.pow(2, 2);
}

0.1 + 0.2 === 0.3 // false
withinErrorMargin(0.1 + 0.2, 0.3) // true
```

### 安全整数和 Number.isSafeInteger()
JavaScript 能够准确表示的整数范围在-2^53到2^53之间（不含两个端点），超过这个范围，无法精确表示这个值。
Number.MAX_SAFE_INTEGER和Number.MIN_SAFE_INTEGER这两个常量，用来表示这个范围的上下限。
```
Number.MAX_SAFE_INTEGER === Math.pow(2, 53) - 1
// true
Number.MAX_SAFE_INTEGER === 9007199254740991
// true

Number.MIN_SAFE_INTEGER === -Number.MAX_SAFE_INTEGER
// true
Number.MIN_SAFE_INTEGER === -9007199254740991
// true
```

不要只验证运算结果，而要同时验证参与运算的每个值。

### Math 对象的扩展
- Math.trunc 去除小数部分
- Math.sign 正数+1，负数-1，零0，负零-0，其他值NaN。
- Math.cbrt 立方根
- Math.clz32 JavaScript的整数使用 32 位二进制形式表示，Math.clz32方法返回一个数的 32 位无符号整数形式有多少个前导 0。
- Math.imul 返回两个数以 32 位带符号整数形式相乘的结果。
- Math.fround 返回一个数的32位单精度浮点数形式
- Math.hypot 返回所有参数的平方和的平方根。
- Math.expm1 返回 ex - 1，即Math.exp(x) - 1。
- Math.log1p 返回1 + x的自然对数，即Math.log(1 + x)。
- Math.log10 返回以 10 为底的x的对数
- Math.log2 返回以 2 为底的x的对数
- ES6 新增了 6 个双曲函数方法

### 指数运算符
指数运算符（`**`）。 `2 ** 3 === 8`

## 函数的扩展
### 函数参数的默认值
- 定义了默认值的参数，应该是函数的尾参数。

- 默认值的两种情况
```
// 写法一: 给结构赋默认值
function m1({x = 0, y = 0} = {}) {
  return [x, y];
}

// 写法二： 给函数参数赋默认值
function m2({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
```

- 函数的length属性，将返回没有指定默认值的参数个数

### rest 参数
用于获取函数的多余参数，这样就不需要使用arguments对象了。
rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。

### 严格模式
规定只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错。

### name 属性
```
var f = function () {};

// ES5
f.name // ""

// ES6
f.name // "f"

const bar = function baz() {};

// ES5
bar.name // "baz"

// ES6
bar.name // "baz"
```

### 箭头函数
```
var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
};
```
代码块部分多于一条语句时需要加大括号。
直接返回一个对象时需要加小括号。
箭头函数有几个使用注意点
1. 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。
2. 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。
3. 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。
4. 不可以使用yield命令，因此箭头函数不能用作 Generator 函数。

### 双冒号运算符
函数绑定运算符是并排的两个冒号（::），双冒号左边是一个对象，右边是一个函数。该运算符会自动将左边的对象，作为上下文环境（即this对象），绑定到右边的函数上面。
```
foo::bar;
// 等同于
bar.bind(foo);

var method = obj::obj.foo;
// 等同于
var method = ::obj.foo;
```

### 尾调用优化
尾调用就是指某个函数的最后一步是调用另一个函数。
ES6 的尾调用优化只在严格模式下开启，正常模式是无效的。
```
function f() {
  let m = 1;
  let n = 2;
  return g(m + n);
}
f();

// 等同于
function f() {
  return g(3);
}
f();

// 等同于
g(3);
```
上面代码中，如果函数g不是尾调用，函数f就需要保存内部变量m和n的值、g的调用位置等信息。但由于调用g之后，函数f就结束了，所以执行到最后一步，完全可以删除f(x)的调用帧，只保留g(3)的调用帧。
这就叫做“尾调用优化”（Tail call optimization），即只保留内层函数的调用帧。如果所有函数都是尾调用，那么完全可以做到每次执行时，调用帧只有一项，这将大大节省内存。这就是“尾调用优化”的意义。

尾递归
函数调用自身，称为递归。如果尾调用自身，就称为尾递归。
递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生“栈溢出”错误（stack overflow）。但对于尾递归来说，由于只存在一个调用帧，所以永远不会发生“栈溢出”错误。
```
function factorial(n) {
  if (n === 1) return 1;
  return n * factorial(n - 1);
}

factorial(5) // 120
```
上面代码是一个阶乘函数，计算n的阶乘，最多需要保存n个调用记录，复杂度 O(n) 。
如果改写成尾递归，只保留一个调用记录，复杂度 O(1) 。
```
function factorial(n, total) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}

factorial(5, 1) // 120
```
完美版
```
function factorial(n, total = 1) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}

factorial(5) // 120
```

不支持该功能的环境中如何做？尾递归之所以需要优化，原因是调用栈太多，造成溢出，那么只要减少调用栈，就不会溢出。怎么做可以减少调用栈呢？就是采用“循环”换掉“递归”。

### 函数参数的尾逗号
允许定义和调用时，尾部直接有一个逗号。

## 数组的扩展
### 扩展运算符
...是rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列
```
Math.max(...[14, 3, 77])

// ES6 的写法
let arr1 = [0, 1, 2];
let arr2 = [3, 4, 5];
arr1.push(...arr2);
```

应用场景：
1. 复制数组
```
const a1 = [1, 2];
// 写法一
const a2 = [...a1];
// 写法二
const [...a2] = a1;
```
2. 合并数组
```
// 产生新的数组
[...arr1, ...arr2, ...arr3]
// arr1就是合并后的结果
arr1.push(...arr2);
```
3. 与解构赋值结合
```
const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]

const [first, ...rest] = [];
first // undefined
rest  // []

const [first, ...rest] = ["foo"];
first  // "foo"
rest   // []
```
4. 字符串
```
[...'hello']
// [ "h", "e", "l", "l", "o" ]
```
5. 实现了 Iterator 接口的对象
```
let nodeList = document.querySelectorAll('div');
let array = [...nodeList];
```
6. Map 和 Set 结构，Generator 函数

### Array.from()
Array.from方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）。
任何有length属性的对象，都可以通过Array.from方法转为数组，而此时扩展运算符就无法转换。
### Array.of()
Array.of方法用于将一组值，转换为数组。
```
Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1

Array() // []
Array(3) // [, , ,]
Array(3, 11, 8) // [3, 11, 8]
```
### 数组实例的 copyWithin()
数组实例的copyWithin方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组。
它接受三个参数:
target（必需）：从该位置开始替换数据。如果为负值，表示倒数。
start（可选）：从该位置开始读取数据，默认为 0。如果为负值，表示倒数。
end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数。
```
// 将3号位复制到0号位
[1, 2, 3, 4, 5].copyWithin(0, 3, 4)
// [4, 2, 3, 4, 5]
```
### 数组实例的 find() 和 findIndex()
indexOf方法无法识别数组的NaN成员
```
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10

[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2
```
### 数组实例的 fill()
fill方法使用给定值，填充一个数组。
```
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

['a', 'b', 'c'].fill(7, 1, 2)
// ['a', 7, 'c']
```
### 数组实例的 entries()，keys() 和 values()
keys()是对键名的遍历、values()是对键值的遍历，entries()是对键值对的遍历
```
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"

let letter = ['a', 'b', 'c'];
let entries = letter.entries();
console.log(entries.next().value); // [0, 'a']
console.log(entries.next().value); // [1, 'b']
console.log(entries.next().value); // [2, 'c']
```
### 数组实例的 includes()
方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的includes方法类似。
indexOf不够语义化，不能判断NaN
### 数组的空位
```
Array(3) // [, , ,]
```
空位不是undefined，一个位置的值等于undefined，依然是有值的。空位是没有任何值。

ES5 对空位的处理，已经很不一致了，大多数情况下会忽略空位（所以避免数组的空位）:
forEach(), filter(), reduce(), every() 和some()都会跳过空位。
map()会跳过空位，但会保留这个值
join()和toString()会将空位视为undefined，而undefined和null会被处理成空字符串。

ES6 则是明确将空位转为undefined。


## 对象的扩展
### 属性的简洁表示法
对象的普通属性和方法属性都可以简写
```
let birth = '2000/01/01';

const Person = {

  name: '张三',

  //等同于birth: birth
  birth,

  // 等同于hello: function ()...
  hello() { console.log('我的名字是', this.name); }

};

// 对象的方法使用了取值函数（getter）和存值函数（setter）对应的简写
const obj = {
  get foo() {},
  set foo(x) {}
};
```

### 属性名表达式
ES6 允许字面量定义对象时，把表达式放在方括号内作为对象的属性名。
```
let propKey = 'foo';

let obj = {
  [propKey]: true,
  ['a' + 'bc']: 123,
  ['h' + 'ello']() {
    return 'hi';
  }
};
```

### 方法的 name 属性
方法的name属性返回函数名（即方法名）。
如果设置了对象的getter、setter，返回值是方法名前加上get和set；
bind方法创造的函数，name属性返回bound加上原函数的名字；
Function构造函数创造的函数，name属性返回anonymous；
如果对象的方法是一个 Symbol 值，那么name属性返回的是这个 Symbol 值的描述。

### Object.is()
Object.is就是部署这个算法的新方法。它用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致。
不同之处只有两个：一是+0不等于-0，二是NaN等于自身。
```
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```
### Object.assign()
Object.assign方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。
```
const target = { a: 1 };

const source1 = { b: 2 };
const source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```
注意：浅拷贝；同名属性的替换；数组按对象处理；如果要复制的值是一个取值函数，那么将求值后再复制；
### 属性的可枚举性
有四个操作会忽略enumerable为false的属性：
for...in循环：只遍历对象自身的和继承的可枚举的属性。
Object.keys()：返回对象自身的所有可枚举的属性的键名。
JSON.stringify()：只串行化对象自身的可枚举的属性。
Object.assign()： 忽略enumerable为false的属性，只拷贝对象自身的可枚举的属性。

对象原型的toString方法，以及数组的length属性，就通过“可枚举性”，从而避免被for...in遍历到。

### 对象遍历
1. for...in：循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。
2. Object.keys(obj)：返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。
3. Object.getOwnPropertyNames：返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。
4. Object.getOwnPropertySymbols：返回一个数组，包含对象自身的所有 Symbol 属性的键名。
5. Reflect.ownKeys(obj)：返回一个数组，包含对象自身的所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。
以上的 5 种方法遍历对象的键名，都遵守同样的属性遍历的次序规则：
首先遍历所有数值键，按照数值升序排列。
其次遍历所有字符串键，按照加入时间升序排列。
最后遍历所有 Symbol 键，按照加入时间升序排列。

### Object.getOwnPropertyDescriptors()
返回指定对象所有自身属性（非继承属性）的描述对象。

### __proto__属性，Object.setPrototypeOf()，Object.getPrototypeOf()
__proto__属性（前后各两个下划线），用来读取或设置当前对象的prototype对象。
无论从语义的角度，还是从兼容性的角度，都不要使用这个__proto__属性，而是使用Object.setPrototypeOf()（写操作）、Object.getPrototypeOf()（读操作）、Object.create()（生成操作）代替。

### super 关键字
指向当前对象的原型对象。
super关键字表示原型对象时，只能用在对象的方法之中，用在其他地方都会报错。只有对象方法的简写法可以让 JavaScript 引擎确认，定义的是对象的方法。

### Object.keys()，Object.values()，Object.entries()
方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的数组。
```
let {keys, values, entries} = Object;
let obj = { a: 1, b: 2, c: 3 };

for (let key of keys(obj)) {
  console.log(key); // 'a', 'b', 'c'
}

for (let value of values(obj)) {
  console.log(value); // 1, 2, 3
}

for (let [key, value] of entries(obj)) {
  console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
}
```

### 对象的扩展运算符
对象的解构赋值用于从一个对象取值，相当于将目标对象自身(不包含继承的)的所有可遍历的（enumerable）、但尚未被读取的属性，分配到指定的对象上面。

## Symbol
### 概述
一种新的原始数据类型Symbol，表示独一无二的值。
### 作为属性名的 Symbol
能防止某一个键被不小心改写或覆盖。
### 实例：消除魔术字符串
魔术字符串指的是，在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值。
```
const shapeType = {
  triangle: Symbol()
};

function getArea(shape, options) {
  let area = 0;
  switch (shape) {
    case shapeType.triangle:
      area = .5 * options.width * options.height;
      break;
  }
  return area;
}

getArea(shapeType.triangle, { width: 100, height: 100 });
```
### 属性名的遍历
Object.getOwnPropertySymbols方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。
Reflect.ownKeys方法可以返回所有类型的键名，包括常规键名和 Symbol 键名。
由于以 Symbol 值作为名称的属性，不会被常规方法遍历得到。我们可以利用这个特性，为对象定义一些非私有的、但又希望只用于内部的方法。

### Symbol.for()，Symbol.keyFor()
Symbol.for方法使用同一个 Symbol 值，生成同一个值。
Symbol.keyFor方法返回一个已登记的 Symbol 类型值的key。

### 实例：模块的 Singleton 模式
```
// mod.js
const FOO_KEY = Symbol.for('foo');

function A() {
  this.foo = 'hello';
}

if (!global[FOO_KEY]) {
  global[FOO_KEY] = new A();
}

module.exports = global[FOO_KEY];
```
上面代码中，可以保证global[FOO_KEY]不会被无意间覆盖，但还是可以被改写。
如果需要修改，可以通过Symbol.for来修改。

### 内置的 Symbol 值
- Symbol.hasInstance
对象的Symbol.hasInstance属性，指向一个内部方法。当其他对象使用instanceof运算符，判断是否为该对象的实例时，会调用这个方法。比如，foo instanceof Foo在语言内部，实际调用的是`Foo[Symbol.hasInstance](foo)`。
```
class MyClass {
  [Symbol.hasInstance](foo) {
    return foo instanceof Array;
  }
}

[1, 2, 3] instanceof new MyClass() // true
```
- Symbol.isConcatSpreadable
对象的Symbol.isConcatSpreadable属性等于一个布尔值，表示该对象用于Array.prototype.concat()时，是否可以展开。
```
let arr1 = ['c', 'd'];
['a', 'b'].concat(arr1, 'e') // ['a', 'b', 'c', 'd', 'e']
arr1[Symbol.isConcatSpreadable] // undefined

let arr2 = ['c', 'd'];
arr2[Symbol.isConcatSpreadable] = false;
['a', 'b'].concat(arr2, 'e') // ['a', 'b', ['c','d'], 'e']
```
- Symbol.species
Symbol.species的作用在于，实例对象在运行过程中，需要再次调用自身的构造函数时，会调用该属性指定的构造函数。它主要的用途是，有些类库是在基类的基础上修改的，那么子类使用继承的方法时，作者可能希望返回基类的实例，而不是子类的实例。

- Symbol.match,Symbol.replace,Symbol.search,Symbol.split
对象的属性，指向一个函数。当执行str.match(myObject)/str.replace(myObject)/str.search(myObject)/str.split(myObject)时，如果该属性存在，会调用它，返回该方法的返回值。

- Symbol.iterator
对象的Symbol.iterator属性，指向该对象的默认遍历器方法。

- Symbol.toPrimitive
对象的Symbol.toPrimitive属性，指向一个方法。该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。

- Symbol.toStringTag
对象的Symbol.toStringTag属性，指向一个方法。在该对象上面调用Object.prototype.toString方法时，如果这个属性存在，它的返回值会出现在toString方法返回的字符串之中，表示对象的类型。

- Symbol.unscopables
对象的Symbol.unscopables属性，指向一个对象。该对象指定了使用with关键字时，哪些属性会被with环境排除。

## Set 和 Map 数据结构
### Set
它类似于数组，但是成员的值都是唯一的，没有重复的值。
Set 函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。
Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（===），主要的区别是NaN等于自身，而精确相等运算符认为NaN不等于自身。
四种操作方法：
- add(value)：添加某个值，返回 Set 结构本身。
- delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
- has(value)：返回一个布尔值，表示该值是否为Set的成员。
- clear()：清除所有成员，没有返回值。
四种遍历方法：（按照插入顺序遍历）
- keys()：返回键名的遍历器
- values()：返回键值的遍历器
- entries()：返回键值对的遍历器
- forEach()：使用回调函数遍历每个成员
### WeakSet
WeakSet 的成员只能是对象，而不能是其他类型的值。
不存在size，不能遍历。
WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。
### Map
它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。如果你需要“键值对”的数据结构，Map 比 Object 更合适。

size,set(),keys(),has(),delete(),clear()
keys()，values()，entries()，forEach()

map与array,object,json之间的相互转换
### WeakMap
WeakMap只接受对象作为键名（null除外），不接受其他类型的值作为键名。
WeakMap的键名所指向的对象，不计入垃圾回收机制。
get()、set()、has()、delete()

## Proxy
代理器。在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。
### 概述
```
var obj = new Proxy({}, {
  get: function (target, key, receiver) {
    console.log(`getting ${key}!`);
    return Reflect.get(target, key, receiver);
  },
  set: function (target, key, value, receiver) {
    console.log(`setting ${key}!`);
    return Reflect.set(target, key, value, receiver);
  }
});
```
### Proxy 实例的方法
- get(target, propKey, receiver)：拦截对象属性的读取，比如proxy.foo和proxy['foo']。
- set(target, propKey, value, receiver)：拦截对象属性的设置，比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。
- has(target, propKey)：拦截propKey in proxy的操作，返回一个布尔值。
- deleteProperty(target, propKey)：拦截delete proxy[propKey]的操作，返回一个布尔值。
- ownKeys(target)：拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)、for...in循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。
- getOwnPropertyDescriptor(target, propKey)：拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。
defineProperty(target, propKey, propDesc)：拦截Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值。
preventExtensions(target)：拦截Object.preventExtensions(proxy)，返回一个布尔值。
getPrototypeOf(target)：拦截Object.getPrototypeOf(proxy)，返回一个对象。
isExtensible(target)：拦截Object.isExtensible(proxy)，返回一个布尔值。
setPrototypeOf(target, proto)：拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
apply(target, object, args)：拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
construct(target, args)：拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。
### Proxy.revocable()
取消Proxy实例。
```
let target = {};
let handler = {};

let {proxy, revoke} = Proxy.revocable(target, handler);

proxy.foo = 123;
proxy.foo // 123

revoke();
proxy.foo // TypeError: Revoked
```
### this 问题
在 Proxy 代理的情况下，目标对象内部的this关键字会指向 Proxy 代理。
### 实例：Web 服务的客户端

## Reflect
### 概述
- 将Object对象的一些明显属于语言内部的方法（比如Object.defineProperty），放到Reflect对象上。
- 修改某些Object方法的返回结果，让其变得更合理。
- 让Object操作都变成函数行为。
- 让Proxy对象可以方便地调用对应的Reflect方法，完成默认行为，作为修改行为的基础。

```
// 老写法
Function.prototype.apply.call(Math.floor, undefined, [1.75]) // 1

// 新写法
Reflect.apply(Math.floor, undefined, [1.75]) // 1
```

### 静态方法
Reflect.apply(target, thisArg, args)
Reflect.construct(target, args)
Reflect.get(target, name, receiver)
Reflect.set(target, name, value, receiver)
Reflect.defineProperty(target, name, desc)
Reflect.deleteProperty(target, name)
Reflect.has(target, name)
Reflect.ownKeys(target)
Reflect.isExtensible(target)
Reflect.preventExtensions(target)
Reflect.getOwnPropertyDescriptor(target, name)
Reflect.getPrototypeOf(target)
Reflect.setPrototypeOf(target, prototype)
### 实例：使用 Proxy 实现观察者模式
```
const person = observable({
  name: '张三',
  age: 20
});

function print() {
  console.log(`${person.name}, ${person.age}`)
}

observe(print);
person.name = '李四';
// 输出
// 李四, 20

const queuedObservers = new Set();

const observe = fn => queuedObservers.add(fn);
const observable = obj => new Proxy(obj, {set});

function set(target, key, value, receiver) {
  const result = Reflect.set(target, key, value, receiver);
  queuedObservers.forEach(observer => observer());
  return result;
}
```

## Promise 对象
推荐阅读![透彻掌握Promise的使用，读这篇就够了](http://caibaojian.com/toutiao/7312)
### Promise 的含义
Promise 是异步编程的一种解决方案，比传统的解决方案“回调函数和事件”更合理和更强大。
回调地狱。
数据请求与数据处理明确的区分开来。
所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。
Promise 内部的错误不会影响到 Promise 外部的代码，通俗的说法就是“Promise 会吃掉错误”。
### 基本用法
```
const getJSON = function(url) {
  const promise = new Promise(function(resolve, reject){
    const handler = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    const client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();

  });

  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
```
### then()和catch()
```
// bad
promise
  .then(function(data) {
    // success
  }, function(err) {
    // error
  });

// good
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
```
### Promise.prototype.finally()
finally方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。
### Promise.all()
将多个 Promise 实例，包装成一个新的 Promise 实例。
只要p1、p2、p3之中全部fulfilled，p的状态fulfilled；之中有一个rejected，p的状态就rejected。
如果作为参数的 Promise 实例，自己定义了catch方法，那么它一旦被rejected，并不会触发Promise.all()的catch方法。
### Promise.race()
将多个 Promise 实例，包装成一个新的 Promise 实例。
只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。
### Promise.resolve()
### Promise.reject()
### 应用
### Promise.try()
