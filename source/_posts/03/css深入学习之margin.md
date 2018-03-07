---
title: css深入学习之margin
date: 2018-03-02 10:12:41
tags: [css]
header-img: "head.jpg"
---

# css深入学习之margin

## 与容器尺寸
可视尺寸，占据尺寸

利用margin可以改变可视尺寸大小的特性
改变水平方向的尺寸（容器大小没有设置固定值，普通block水平元素
用途：一侧定宽，另一侧自适应。两端对其布局。

利用margin可以改变占据区域尺寸大小的特性
滚动容器实现上下留白，padding在火狐无效，所以用margin

## 与百分比单位
普通元素百分比计算规则，水平垂直方向的百分比值计算都是相对于父级宽度计算的。
绝对定位元素则是相对于第一个定位祖先元素的宽度计算的

用途：宽高2:1的自适应矩形。 

## margin重叠
发生在block水平元素，只发生在垂直方向的（不考虑writing-mode改变文字书写方向）

三种情景：
- 相邻的兄弟
- 父级和第一个最后一个子元素
margin-top重叠条件
父元素非块状格式化上下文
父元素没有border-top设置
父元素没有padding-top值
父元素和第一个子元素没有行内元素分离
margin-bottom 除了上边四个，还有没有height相关声明
- 空的block元素
条件：没有border设置，没有padding 值，里面没有行内元素，没有高度相关的设置

计算规则：
正正取大值，正负相加，负负取最小值

原因：网页诞生之初，多是文章文字的展示，margin重叠会使文字排版更好。

使用：表单等列表时，最好上下都设置margin，这样新增删除某一项时，不会影响布局

## margin auto
宽度自动填充，如果设置了固定宽度，那么这一行就会产生剩余空间，margin auto就是设置这一部分的。

auto就是自动填充，设置auto就是剩余空间大小。

问题：
- 图片不水平居中，因为图片是行内元素。他没有剩余空间，解决办法就是把它设置为block块状元素。
- 垂直布局中，因为他高度不会自动填充，没有剩余空间。解决办法：设置writing-mode为垂直，但是水平就失效了。绝对定位，让它覆盖父级，然后设置大小，设置margin auto就行了。

## 负值定位
两端对齐：横向列表，两端对齐。等高布局。两栏自适应布局。

dom顺序应与视图顺序不符，这是不好的。

## margin无效
- 内敛元素（非替换元素，没有改变流方向）垂直方向margin无效。
- margin重叠
- display为table-cell，table-row等。替换元素除外。

火狐下table-cell类型表现与inline-block相同。IE下table-cell表现正常。chrome 下button即使设置了table-cell都会被转换为inline-block，其他的表现正常。

- 绝对定位元素非定位方向的margin“无效”（看起来）。实际上是都有效的。
- 鞭长莫及。当有浮动元素时，margin不会相对于浮动元素去margin。
- 内敛特性，基线对齐，所以当margin再怎么小，也小不过基线（文字的下边线）

## 其他
正常流margin-start相当于margin-left，两者重叠，不累加。
水平流从右向左时，相当于margin-right
垂直流下，相当于margin-top

只有在webkit内核下有效。margin-before，margin-after，margin-collapse。

以上内容来自张鑫旭老师在慕课网上的课程
