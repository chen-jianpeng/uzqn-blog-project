---
title: css深入学习之z-index
date: 2018-03-04 09:44:26
tags: css
header-img: "head.jpg"
---
# css深入学习之z-index

z-index支持负值，支持长css3 animation动画，css2.1时需要和定位元素配合使用。

## 与css定位属性
没有嵌套：后来居上，谁大谁上。
嵌套：祖先优先原则。

例子：
祖先优先原则失效了？
z-index为auto时，当前层叠上下文的生成盒子层叠水平是0，盒子（除非是根元素）不会创建一个新的层叠上下文。
所以导致block1的z-index生效了。

<iframe height='265' scrolling='no' title='css深入学习之z-index' src='//codepen.io/chenjp/embed/aqrEzN/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/aqrEzN/'>css深入学习之z-index</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 层叠上下文和层叠水平

层叠上下文表示谁离皇帝更近，层叠水平决定谁离官员更近

### 层叠上下文：

1. 概念：
    三维概念，表示普通元素高人一等了。
2. 存在：
    页面根元素天生有层叠上下文。
    z-index为数值的定位元素也有层叠上下文。
    设置其他属性，使得其存在。(包括设置了flex，opacity不为1，tansform：roatate，filter等)
3. 特性：
    可嵌套。
    每个层叠上下文和兄弟元素独立。
    每个层叠上下文都自成体系，元素内容被层叠之后，整个元素被认为是在父层的层叠顺序中。

### 层叠水平：

1. 层叠上下文中的每个元素都有一个层叠水平。
2. 层叠水平就是普通元素的论资排辈
3. 层叠水平和z-index不是一个东西

## 层叠顺序

1. 定义：元素发生层叠时候有着特定的垂直显示顺序

2. 七阶层叠水平：
装饰：
层叠上下文的background/border
负z-index，
布局：
block块状水平盒子，
float浮动盒子，
内容：
inline/inline-block水平盒子，
z-index:auto或看成z-index:0，不依赖z-index的层叠上下文（就是设置了除z-index以外的属性产生的层叠上下文）
正z-index

    ![七阶层叠水平](七阶层叠顺序.png)
    
3. 意义：规范元素重叠时候的呈现规则

4. 为什么有七阶层叠水平？
符合页面加载和页面呈现。内容最重要。

例子：
背景色是因为inline-block的层级更高，文字都是内联元素遵循后来居上。

<iframe height='265' scrolling='no' title='七阶层叠水平1' src='//codepen.io/chenjp/embed/rJgYdg/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/rJgYdg/'>七阶层叠水平1</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## z-index与层叠上下文

1. 定位元素默认z-index：auto可以看成z-index：0
2. z-index不为auto的定位元素会创建层叠上下文
3. z-index层叠顺序的比较止步于父级层叠上下文

例子1：
为何定位元素会覆盖普通元素？因为定位元素未设置z-index时就是auto，根据七阶层叠水平得到，其层级高于inline-block元素。

<iframe height='265' scrolling='no' title='z-index与层叠上下文1' src='//codepen.io/chenjp/embed/GQaOgq/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/GQaOgq/'>z-index与层叠上下文1</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

例子2：
block的z-index为-1，找到他的第一个父级层叠上下文元素就是wrap，根据七阶层叠水平z-index为负，但仍高于背景色，所以产生如下效果

<iframe height='265' scrolling='no' title='z-index与层叠上下文2' src='//codepen.io/chenjp/embed/jZoaVx/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/jZoaVx/'>z-index与层叠上下文2</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

例子3：
受制于父级层叠上下文

<iframe height='265' scrolling='no' title='z-index与层叠上下文3' src='//codepen.io/chenjp/embed/WMBXXJ/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/WMBXXJ/'>z-index与层叠上下文3</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## z-index与非定位元素层叠上下文

1. 不支持z-index的层叠上下文元素的层叠顺序均是z-index=auto的级别
2. 依赖z-index的层叠上下文，层叠顺序依赖z-index值。包括两种情况：
* position为relative/absolute或者fixed（但是chrome下，fixed元素不需要设置z-index即可创建层叠上下文）
* display为flex/inline-flex容器的子flex项

例子1：
除了第一个div，其他的都创建了层叠上下文，z-index都是auto，遵循后来居上，所以产生如下效果

<iframe height='265' scrolling='no' title='z-index与非定位元素层叠上下文1' src='//codepen.io/chenjp/embed/LQoyBv/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/LQoyBv/'>z-index与非定位元素层叠上下文1</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

例子2：
由于block1设置了display：absolute，block2设置了flex，都创建了层叠上下文，层级相同，但背景色的七阶水平更低，所以在下边。

<iframe height='265' scrolling='no' title='z-index与非定位元素层叠上下文2' src='//codepen.io/chenjp/embed/yvWXEZ/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/yvWXEZ/'>z-index与非定位元素层叠上下文2</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

例子3：
图片设置一个淡出的效果，文字在div完全显示后才显示。原因是opacity不是1时会创建层叠上下文，这是他的层级与文字的层级是一样的，相当于z-index：auto，遵循后来居上，所以div遮住文字。当opacity为1时，div变成普通元素，所以文字在div之上。

<iframe height='265' scrolling='no' title='z-index与非定位元素层叠上下文 3' src='//codepen.io/chenjp/embed/vdwmxw/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/vdwmxw/'>z-index与非定位元素层叠上下文 3</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## z-index使用忠告
1. 最小化原则，能不用就别用
2. 不随意设置，尽量不超过2，使用后来居上覆盖
3. 组件层级计数器，通过js设置z-index。
4. 通过设置z-index使元素可访问性隐藏

以上内容来自张鑫旭老师在慕课网上的课程