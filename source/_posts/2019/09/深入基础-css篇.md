---
title: 深入基础-css篇
date: 2019-09-15 08:47:26
tags: [面试, javascript]
header-img: "head.jpg"
---

## 盒模型

### 类别

#### W3C 标准盒模型

##### width=content

#### IE 盒模型

##### width = content+padding+border

### box-sizing

#### content-box W3C 标准盒模型

#### border-box IE 盒模型

### 组成

#### content+padding+border+margin

## 单位

### px

```html
<meta name="viewport" content="width=device-width,initial-scale=1.0" />
```

content 属性值 :

width:可视区域的宽度，值可为数字或关键词 device-width
height:可视区域的高度，值可为数字或关键词 device-height
intial-scale:页面首次被显示是可视区域的缩放级别，取值 1.0 则页面按实际尺寸显示，无任何缩放
maximum-scale=1.0, minimum-scale=1.0;可视区域的缩放级别，
maximum-scale 用户可将页面放大的程序，1.0 将禁止用户放大到实际尺寸之上。
user-scalable:是否可对页面进行缩放，no 禁止缩放

### em

#### 相对于本身或父容器字体大小

### rem

```js
const fontFun = function() {
  let docEl = document.documentElement;
  let resizeEvt =
    "orientationchange" in window ? "orientationchange" : "resize";
  const recalc = function() {
    let clientWidth = docEl.clientWidth;
    if (!clientWidth) return;
    docEl.style.fontSize = 100 * (clientWidth / 750) + "px";
  };
  if (!document.addEventListener) return;
  window.addEventListener(resizeEvt, recalc, false);
  window.addEventListener("pageshow", recalc, false);
  document.addEventListener("DOMContentLoaded", recalc, false);
};
```

#### 相对于 HTML 字体大小

### rpx

#### 小程序，按设计稿尺寸即可

## 清除浮动

### clear

#### 作用于和浮动元素同级的元素

### overflow

作用于父级元素

### 伪元素

作用于父级元素

```css
.clearFloat::after {
  content: "";
  clear: both;
  display: block;
  height: 0;
  visibility: hidden;
}
```

## BFC

块级格式化上下文。
它是页面中的一块渲染区域，
有一套渲染规则，
它决定了其子元素将如何定位，
以及和其他元素的关系和相互作用。

“封闭的大箱子”
具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。

### 触发

#### body 根元素

#### 浮动元素：float 除 none 以外的值

#### 绝对定位元素：position (absolute、fixed)

#### display 为 inline-block、table-cells、flex

#### overflow 除了 visible 以外的值 (hidden、auto、scroll)

#### 弹性盒子 flex

### 作用

#### 同一个 BFC 容器会发生折叠，不同 BFC 容器不会折叠

#### 清除浮动

#### 实现一列顶宽，另一列自适应布局

```html
<div style="height: 100px;width: 100px;float: left;background: lightblue">
  我是一个左浮动的元素
</div>
<div style="width: 200px; height: 200px;background: #eee">
  我是一个没有设置浮动, 也没有触发 BFC 元素, width: 200px; height:200px;
  background: #eee;
</div>
```

上边的代码将产生文字环绕效果。
将第二个元素添加 overflow:auto 实现一列定宽，另一列自适应布局。

## 布局

### 居中

#### 水平居中

##### 定宽块状元素：margin auto

##### 行内元素：父元素 text-align:center

##### 非定宽块状元素：父容器 text-align:center，自身 display:inline

##### 通用：父容器 flex 布局，justify-content:center

#### 垂直居中

##### 内联单行： line-height 等于 height

##### 内联多行：父容器 display:table。自身 display:table；vertical-align:middle

##### 块状元素：父容器非 static，自身 absolute，top、bottom 为 0，margin:auto

##### 通用： 父容器 flex，align-items:center

### 单列布局

### 两列、三列布局

#### float+margin

注意顺序：先两侧，后中间。
这样就没有优先渲染你最重要的部分了。

```html
<div class="content">
  <div class="sub">sub</div>
  <div class="extra">extra</div>
  <div class="main">main</div>
</div>
```

```css
.sub {
  width: 200px;
  float: left;
}
.extra {
  width: 200px;
  float: right;
}
.main {
  margin: 0 200px;
}
```

#### position+margin

无书 DOM 写顺序限制
如果中间栏含有最小宽度限制，或是含有宽度的内部元素，则浏览器窗口小到一定程度，主面板与侧栏会发生重叠。

```html
<div class="content">
  <div class="main">main</div>
  <div class="sub">sub</div>
  <div class="extra">extra</div>
</div>
```

```css
.content {
  position: relative;
}
.sub {
  width: 200px;
  position: absolute;
  top: 0;
  left: 0;
}
.extra {
  width: 200px;
  position: absolute;
  top: 0;
  right: 0;
}
.main {
  margin: 0 200px;
}
```

#### 圣杯布局


dom 顺序不可变。当中间区域小于两侧区域宽度时布局会错乱。限制 content 最小宽度。

```html
<div class="content">
  <div class="main">main</div>
  <div class="sub">sub</div>
  <div class="extra">extra</div>
</div>
```

```css
.content {
  padding: 0 200px;
}
.main {
  float: left;
  width: 100%;
}
.sub {
  width: 200px;
  float: left;
  margin-left: -100%;
  position: relative;
  left: -200px;
}
.extra {
  width: 200px;
  float: right;
  margin-right: -200px;
  position: relative;
  right: -200px;
}
```

#### 双飞翼布局

```html
<div class="content">
  <div class="main-wrap">
    <div class="main">
      main
    </div>
  </div>
  <div class="sub">sub</div>
  <div class="extra">extra</div>
  <div></div>
</div>
```

```css
.main-wrap {
  float: left;
  width: 100%;
}
.main {
  margin: 0 200px 0 290px;
}
.sub {
  float: left;
  width: 290px;
  margin-left: -100%;
}
.extra {
  float: left;
  width: 200px;
  margin-left: -200px;
}
```

#### flex 布局

## 杂记

margin/padding 的 top/right/bottom/left 百分比值都根据父容器 content 的宽度决定

## 弹性布局 flex

在 flex 为 1 的容器中，子元素 height：100%，但子元素内内容超过 100%后，子容器会大于 100%的 bug。通过 absolute 可以解决此问题。
[参考链接](http://jsfiddle.net/poztin/MFHj7/1/)

### 作用于父容器

#### flex-flow

##### flex-direction

row
row-reverse
column
column-reverse

##### flex-wrap

nowrap
wrap
wrap-reverse

#### justify-content

flex-start
center
flex-end
space-between
space-around

#### align-items

flex-start
center
flex-end
baseline
stretch

#### align-content （多跟轴线的对其方式）

flex-start
center
flex-end
space-between
space-around
stretch

### 作用于子元素

#### 分支主题

##### order

数字越小越靠前

##### flex

###### flex-grow

默认值为 0，有空间也不占

不为零时按占比分配大小

###### flex-shrink

默认值为 1，空间不足时将缩小

为零时不缩小

一个为 1，一个为 2 的两个 div
缩小时值越大的缩小越快

###### flex-basic

##### align-self

auto
flex-start
flex-end
center
baseline
stretch

## css3

### animation

## 伪类、伪元素

### 伪类

定义的是状态

:root 选择文档的根元素，等同于 html 元素
:empty 选择没有子元素的元素
:target 选取当前活动的目标元素
:not(selector) 选择除 selector 元素意外的元素
:enabled 选择可用的表单元素
:disabled 选择禁用的表单元素
:checked 选择被选中的表单元素
:nth-child(n) 匹配父元素下指定子元素，在所有子元素中排序第 n
:nth-last-child(n) 匹配父元素下指定子元素，在所有子元素中排序第 n，从后向前数
:nth-child(odd) 、 :nth-child(even) 、 :nth-child(3n+1)
:first-child 、 :last-child 、 :only-child
:nth-of-type(n) 匹配父元素下指定子元素，在同类子元素中排序第 n
:nth-last-of-type(n) 匹配父元素下指定子元素，在同类子元素中排序第 n，从后向前数
:nth-of-type(odd) 、 :nth-of-type(even) 、 :nth-of-type(3n+1)
:first-of-type 、 :last-of-type 、 :only-of-type

### 伪元素

不存在于 DOM 树中的虚拟元素，它们可以像正常的 html 元素一样定义 css，但无法使用 JavaScript 获取。

::after 已选中元素的最后一个子元素
::before 已选中元素的第一个子元素
::first-letter 选中某个款级元素的第一行的第一个字母
::first-line 匹配某个块级元素的第一行
::selection 匹配用户划词时的高亮部分

## 层叠上下文
