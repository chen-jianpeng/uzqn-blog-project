---
title: 深入基础-js篇-异步
date: 2019-09-15 08:47:26
tags: [面试, javascript]
header-img: "head.jpg"
---

异步编程的三个阶段

## promise

- promise 是一个存放着异步操作结果的容器，是一个可以从他获取异步操作消息的对象。
- Promise 是生成 promise 的构造函数。
- 三种状态：pending（进行中）、fulfilled（已成功）、reject（已失败）。状态一旦改变就不会再变了。
- promise 新建后就会立即执行

### 常用方法

#### then

```js
function start() {
  return new Promise((resolve, reject) => {
    resolve("start");
  });
}

start()
  .then(data => {
    // promise start
    console.log("result of start: ", data);
    return Promise.resolve(1); // p1
  })
  .then(data => {
    // promise p1
    console.log("result of p1: ", data);
    return Promise.reject(2); // p2
  })
  .then(data => {
    // promise p2
    console.log("result of p2: ", data);
    return Promise.resolve(3); // p3
  })
  .catch(ex => {
    // promise p3
    console.log("ex: ", ex);
    return Promise.resolve(4); // p4
  })
  .then(data => {
    // promise p4
    console.log("result of p4: ", data);
  });

// 上面的代码最终会输出：
// result of start:  start
// result of p1:  1
// ex:  2
// result of p4:  4
```

#### finally

#### all

#### race

#### resolve

#### reject

#### try

同步函数同步执行，异步函数异步执行

### 实现原理

```js
// 判断变量否为function
const isFunction = variable => typeof variable === "function";
// 定义Promise的三种状态常量
const PENDING = "PENDING";
const FULFILLED = "FULFILLED";
const REJECTED = "REJECTED";

class MyPromise {
  constructor(handle) {
    if (!isFunction(handle)) {
      throw new Error("MyPromise must accept a function as a parameter");
    }
    // 添加状态
    this._status = PENDING;
    // 添加状态
    this._value = undefined;
    // 添加成功回调函数队列
    this._fulfilledQueues = [];
    // 添加失败回调函数队列
    this._rejectedQueues = [];
    // 执行handle
    try {
      handle(this._resolve.bind(this), this._reject.bind(this));
    } catch (err) {
      this._reject(err);
    }
  }
  // 添加resovle时执行的函数
  _resolve(val) {
    const run = () => {
      if (this._status !== PENDING) return;
      // 依次执行成功队列中的函数，并清空队列
      const runFulfilled = value => {
        let cb;
        while ((cb = this._fulfilledQueues.shift())) {
          cb(value);
        }
      };
      // 依次执行失败队列中的函数，并清空队列
      const runRejected = error => {
        let cb;
        while ((cb = this._rejectedQueues.shift())) {
          cb(error);
        }
      };
      /* 如果resolve的参数为Promise对象，则必须等待该Promise对象状态改变后,
          当前Promsie的状态才会改变，且状态取决于参数Promsie对象的状态
        */
      if (val instanceof MyPromise) {
        val.then(
          value => {
            this._value = value;
            this._status = FULFILLED;
            runFulfilled(value);
          },
          err => {
            this._value = err;
            this._status = REJECTED;
            runRejected(err);
          }
        );
      } else {
        this._value = val;
        this._status = FULFILLED;
        runFulfilled(val);
      }
    };
    // 为了支持同步的Promise，这里采用异步调用
    setTimeout(run, 0);
  }
  // 添加reject时执行的函数
  _reject(err) {
    if (this._status !== PENDING) return;
    // 依次执行失败队列中的函数，并清空队列
    const run = () => {
      this._status = REJECTED;
      this._value = err;
      let cb;
      while ((cb = this._rejectedQueues.shift())) {
        cb(err);
      }
    };
    // 为了支持同步的Promise，这里采用异步调用
    setTimeout(run, 0);
  }
  // 添加then方法
  then(onFulfilled, onRejected) {
    const { _value, _status } = this;
    // 返回一个新的Promise对象
    return new MyPromise((onFulfilledNext, onRejectedNext) => {
      // 封装一个成功时执行的函数
      let fulfilled = value => {
        try {
          if (!isFunction(onFulfilled)) {
            onFulfilledNext(value);
          } else {
            let res = onFulfilled(value);
            if (res instanceof MyPromise) {
              // 如果当前回调函数返回MyPromise对象，必须等待其状态改变后在执行下一个回调
              res.then(onFulfilledNext, onRejectedNext);
            } else {
              //否则会将返回结果直接作为参数，传入下一个then的回调函数，并立即执行下一个then的回调函数
              onFulfilledNext(res);
            }
          }
        } catch (err) {
          // 如果函数执行出错，新的Promise对象的状态为失败
          onRejectedNext(err);
        }
      };
      // 封装一个失败时执行的函数
      let rejected = error => {
        try {
          if (!isFunction(onRejected)) {
            onRejectedNext(error);
          } else {
            let res = onRejected(error);
            if (res instanceof MyPromise) {
              // 如果当前回调函数返回MyPromise对象，必须等待其状态改变后在执行下一个回调
              res.then(onFulfilledNext, onRejectedNext);
            } else {
              //否则会将返回结果直接作为参数，传入下一个then的回调函数，并立即执行下一个then的回调函数
              onFulfilledNext(res);
            }
          }
        } catch (err) {
          // 如果函数执行出错，新的Promise对象的状态为失败
          onRejectedNext(err);
        }
      };
      switch (_status) {
        // 当状态为pending时，将then方法回调函数加入执行队列等待执行
        case PENDING:
          this._fulfilledQueues.push(fulfilled);
          this._rejectedQueues.push(rejected);
          break;
        // 当状态已经改变时，立即执行对应的回调函数
        case FULFILLED:
          fulfilled(_value);
          break;
        case REJECTED:
          rejected(_value);
          break;
      }
    });
  }
  // 添加catch方法
  catch(onRejected) {
    return this.then(undefined, onRejected);
  }
  // 添加静态resolve方法
  static resolve(value) {
    // 如果参数是MyPromise实例，直接返回这个实例
    if (value instanceof MyPromise) return value;
    return new MyPromise(resolve => resolve(value));
  }
  // 添加静态reject方法
  static reject(value) {
    return new MyPromise((resolve, reject) => reject(value));
  }
  // 添加静态all方法
  static all(list) {
    return new MyPromise((resolve, reject) => {
      /**
       * 返回值的集合
       */
      let values = [];
      let count = 0;
      for (let [i, p] of list.entries()) {
        // 数组参数如果不是MyPromise实例，先调用MyPromise.resolve
        this.resolve(p).then(
          res => {
            values[i] = res;
            count++;
            // 所有状态都变成fulfilled时返回的MyPromise状态就变成fulfilled
            if (count === list.length) resolve(values);
          },
          err => {
            // 有一个被rejected时返回的MyPromise状态就变成rejected
            reject(err);
          }
        );
      }
    });
  }
  // 添加静态race方法
  static race(list) {
    return new MyPromise((resolve, reject) => {
      for (let p of list) {
        // 只要有一个实例率先改变状态，新的MyPromise的状态就跟着改变
        this.resolve(p).then(
          res => {
            resolve(res);
          },
          err => {
            reject(err);
          }
        );
      }
    });
  }
  finally(cb) {
    return this.then(
      value => MyPromise.resolve(cb()).then(() => value),
      reason =>
        MyPromise.resolve(cb()).then(() => {
          throw reason;
        })
    );
  }
}
```

## generator

```js
function* gen(arg) {
  yield 2;
  yield arg;
}

let genHandle = gen(3);
for (let i of genHandle) {
  console.log(i); // 依次打印：2，3
}

let genHandle2 = gen(4);
console.log(genHandle2.next()); // { value: 2, done: false }
console.log(genHandle2.next()); // { value: 4, done: false }
console.log(genHandle2.next()); // { value: undefined, done: true }
```

转换成 es5 的代码大致为下面的情况

```js
var context = {
  next: 0,
  prev: null,
  gen$: null,
  done: false,
  stop: function() {
    this.done = true;
  }
};
Object.defineProperty(context, Symbol.iterator, {
  enumerable: false,
  writable: false,
  configurable: true,
  value: function() {
    var me = this;
    return {
      next: function() {
        var nextValue = me.gen$(me);
        return {
          value: nextValue,
          done: me.done
        };
      }
    };
  }
});
var regeneratorRuntime = {
  wrap: function(_gen) {
    context.gen$ = _gen;
    return context;
  }
};
function gen(arg) {
  return regeneratorRuntime.wrap(function gen$(_context) {
    while (1) {
      switch ((_context.prev = _context.next)) {
        case 0:
          _context.next = 2;
          return 2;
        case 2:
          _context.next = 4;
          return arg;
        case 4:
        case "end":
          return _context.stop();
      }
    }
  });
}
var b = gen(3);
for (var c of b) {
  console.log(c); // 结果为：2，3
}
```

### 自动执行

#### 基于 thunk

```js
function run(gen) {
  var g = gen();

  function next(data) {
    var result = g.next(data);
    if (result.done) return result.value;
    result.value(next);
  }

  next();
}

function* g() {
  // ...
}

run(g);
```

#### 基于 promise

```js
function run(gen) {
  var g = gen();

  function next(data) {
    var result = g.next(data);
    if (result.done) return result.value;
    result.value.then(function(data) {
      next(data);
    });
  }

  next();
}

function* g() {
  // ...
}

run(g);
```

## async await

就是 Generator 函数的语法糖。

### 优势

1. 内置执行器。像普通函数一样，一行一行的执行，不必像 generator 函数需要调用 next 函数一步一步执行。
2. 更好的语义。async 替换\*，await 替换 yield
3. 更广的适用性：await 之后可以跟 promise、原始类型
4. 返回值是 promise

### 注意

1. 放在 try catch 中
2. 不是继发关系最好同时执行
3. async 函数可以保留运行堆栈

### 原理

将 Generator 函数和自动执行器，包装在一个函数里。

```js
async function fn(args) {
  // ...
}

// 等同于

function fn(args) {
  return spawn(function*() {
    // ...
  });
}

function spawn(genF) {
  return new Promise(function(resolve, reject) {
    const gen = genF();
    function step(nextF) {
      let next;
      try {
        next = nextF();
      } catch (e) {
        return reject(e);
      }
      if (next.done) {
        return resolve(next.value);
      }
      Promise.resolve(next.value).then(
        function(v) {
          step(function() {
            return gen.next(v);
          });
        },
        function(e) {
          step(function() {
            return gen.throw(e);
          });
        }
      );
    }

    // 传名调用而非传值调用
    step(function() {
      return gen.next(undefined);
    });
  });
}
```
