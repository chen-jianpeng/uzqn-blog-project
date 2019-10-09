---
title: 深入基础-js篇-执行机制
date: 2019-09-15 08:47:26
tags: [面试, javascript]
header-img: "head.jpg"
---

## EventLoop

[带你彻底弄懂 Event Loop](https://juejin.im/post/5b8f76675188255c7c653811)
[微任务、宏任务与 Event-Loop](https://juejin.im/post/5b73d7a6518825610072b42b)

js 是单线程的。多线程会存在一个删除 Dom 节点，一个新增 Dom 节点该听谁的的问题。

1. 执行全局 Script 同步代码，这些同步代码有一些是同步语句，有一些是异步语句（比如 setTimeout 等）；
2. 全局 Script 代码执行完毕后，调用栈 Stack 会清空；
3. 从微队列 microtask queue 中取出位于队首的回调任务，放入调用栈 Stack 中执行，执行完后 microtask queue 长度减 1；
4. 继续取出位于队首的任务，放入调用栈 Stack 中执行，以此类推，直到直到把 microtask queue 中的所有任务都执行完毕。注意，如果在执行 microtask 的过程中，又产生了 microtask，那么会加入到队列的末尾，也会在这个周期被调用执行；
5. microtask queue 中的所有任务都执行完毕，此时 microtask queue 为空队列，调用栈 Stack 也为空；
6. 取出宏队列 macrotask queue 中位于队首的任务，放入 Stack 中执行；
7. 执行完毕后，调用栈 Stack 为空；
8. 重复第 3-7 个步骤；

> 宏队列 macrotask 一次只从队列中取一个任务执行，执行完后就去执行微任务队列中的任务；微任务队列中所有的任务都会被依次取出来执行，知道 microtask queue 为空；

```js
console.log(1);

setTimeout(() => {
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3)
  });
});

new Promise((resolve, reject) => {
  console.log(4)
  resolve(5)
}).then((data) => {
  console.log(data);

  Promise.resolve().then(() => {
    console.log(6)
  }).then(() => {
    console.log(7)

    setTimeout(() => {
      console.log(8)
    }, 0);
  });
})

setTimeout(() => {
  console.log(9);
})

console.log(10);
```

```js
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}
async function async2() {
  console.log("async2");
}
console.log("script start");
setTimeout(function() {
  console.log("setTimeout");
}, 0);
async1();
new Promise(function(resolve) {
  console.log("promise1");
  resolve();
}).then(function() {
  console.log("promise2");
});
console.log("script end");
```

### 同步任务

主线程上排队，一个任务执行完成之后才能执行下一个任务。

### 异步任务

存放于任务队列(消息队列)，当异步任务可执行时加入主线程执行。

IO 事件、用户事件（点击、滚动）等指定了回调函数，这些事件触发之后就会加入到任务队列，等待主线程读取。

#### 宏队列（macrotask、tasks）

一些异步任务的回调会依次进入 macro task queue，等待后续被调用，这些异步任务包括：

setTimeout
setInterval
setImmediate (Node 独有)
I/O
UI rendering (浏览器独有)

#### 微队列（microtask、jobs）

另一些异步任务的回调会依次进入 micro task queue，等待后续被调用，这些异步任务包括：

process.nextTick (Node 独有)
Promise
Object.observe
MutationObserver

## 大数据量渲染

### setInterval是不可靠的

由于 JavaScript 是单线程的，所以定时器的实现是在当前任务队列完成后再执行定时器的回调的，假如当前队列任务执行时间大于定时器设置的延迟时间，那么定时器就不是那么可靠了。

理论上动画保持在平均每秒刷新 60 次的情况下就是流畅的。但 setInterval 无法保证。

### requestAnimationFrame

浏览器在下次重绘之前调用指定的回调函数更新动画。既不是宏任务也不是微任务。

当requestAnimationFrame() 运行在后台标签页或者隐藏的`<iframe>`里时，requestAnimationFrame() 会被暂停调用以提升性能和电池寿命。

CSS3 transition或animation动画也是走的跟你一样的绘制原理。（CSS3动画在Tab切换回来的时候，动画表现并不暂停；Tab切换之后，计算渲染绘制都停止，Tab切换回来时似乎通过内置JS计算了动画位置实现重绘，造成动画不暂停的感觉）。

## 循环异步

### All in （并行）

```js
await Promise.call(times.map(time => sleep(time)));
```

### One by one （串行）

消耗时间场，资源占用小

```js
for (let time of times) {
  await sleep(time);
}
```
