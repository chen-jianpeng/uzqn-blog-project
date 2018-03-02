---
title: css深入学习
date: 2018-03-02 09:44:26
tags: css
header-img: "head.jpg"
---
# css深入学习

## border

### border-width不支持百分比
边框不会随着容器大小的变化而变化
类似的还有outline、box-shadow、text-shadow
关键字thin medium thick
默认值为3，因为border-style：double有效果至少要3像素

### border-style
dashed  虚线
谷歌火狐实色区域宽高3:1，实色和透明色1:1。IE实色宽高2:1，实色透明色2:1
border-emate解决此问题

dotted 点线
谷歌火狐小方，ie小圆

double 双线
计算规则：双线宽度相等，中间区域+-1
实例：实现三道杠的菜单按钮

inset 内凹outset 外凸，山脊山谷很不常用

### border-color color
border-color默认为color的颜色，border-shadow，text-shadow，outline也是
用途：hover变色，只需要在父级上添加即可

### background-position
相对于左上角定位。
实现右下角定位，因为position定位不包括border，所以设置position的x为100%，border为固定值的宽度即可。

### 三角等图形构建
IE78实现圆角
三道杠
三角实现
模拟圆角，上下两个梯形

边框原理!()[]

### 透明边框
增加相应区域大小
filter：drop-shadow改变图片颜色

### 布局使用
border等高布局，不支持百分比
margin与padding实现等高布局

## margin

### 与容器尺寸
可视尺寸，占据尺寸

利用margin可以改变可视尺寸大小的特性
改变水平方向的尺寸（容器大小没有设置固定值，普通block水平元素
用途：一侧定宽，另一侧自适应。两端对其布局。

利用margin可以改变占据区域尺寸大小的特性
滚动容器实现上下留白，padding在火狐无效，所以用margin

### 与百分比单位
普通元素百分比计算规则，水平垂直方向的百分比值计算都是相对于父级宽度计算的。
绝对定位元素则是相对于第一个定位祖先元素的宽度计算的

用途宽高2:1的自适应矩形。 

### margin重叠
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

### margin auto
宽度自动填充，如果设置了固定宽度，那么这一行就会产生剩余空间，margin auto就是设置这一部分的。

auto就是自动填充，设置auto就是剩余空间大小。

问题：
- 图片不水平居中，因为图片是行内元素。他没有剩余空间，解决办法就是把它设置为block块状元素。
- 垂直布局中，因为他高度不会自动填充，没有剩余空间。解决办法：设置writing-mode为垂直，但是水平就失效了。绝对定位，让它覆盖父级，然后设置大小，设置margin auto就行了。

### 负值定位
两端对齐：横向列表，两端对齐。等高布局。两栏自适应布局。

dom顺序应与视图顺序不符，这是不好的。

### margin无效
- 内敛元素（非替换元素，没有改变流方向）垂直方向margin无效。
- margin重叠
- display为table-cell，table-row等。替换元素除外。

火狐下table-cell类型表现与inline-block相同。IE下table-cell表现正常。chrome 下button即使设置了table-cell都会被转换为inline-block，其他的表现正常。

- 绝对定位元素非定位方向的margin“无效”（看起来）。实际上是都有效的。
- 鞭长莫及。当有浮动元素时，margin不会相对于浮动元素去margin。
- 内敛特性，基线对齐，所以当margin再怎么小，也小不过基线（文字的下边线）

### 其他
正常流margin-start相当于margin-left，两者重叠，不累加。
水平流从右向左时，相当于margin-right
垂直流下，相当于margin-top

只有在webkit内核下有效。margin-before，margin-after，margin-collapse。

## padding
### 与容器尺寸
块状元素下
padding 值暴走，一定会影响尺寸。
width为非auto，padding影响尺寸
width为auto，或box-sizing为border-box时，padding 没有暴走，不影响尺寸。

内敛元素下
垂直方向的padding不会影响尺寸，水平方向会。但是会影响背景色（占据空间）
用途：分割线的实现

### padding取值
不支持负值

百分比值相对于宽度计算
用途：实现所有屏幕下都是正方形
内敛元素的padding同样相对于宽度计算，默认的高宽有细节差异，会换行。
宽高差异是因为内敛元素的垂直padding会让‘幽灵空白节点’显现，也就是规范中的strut出现。当font-size为零时，就好了。

### 标签元素的内置padding
ol/ul
ol/li元素内置padding-left，单位是px不是em
谷歌浏览器下是40px
字号很小时，左边间距很开
字号很大时，序号跑到外边。
一般文字不是很大时，padding-left设置为22到25

表单元素
input/textarea输入框内置padding，大约为1到2像素
button元素内置padding
部分浏览器select下拉内置padding，如火狐。IE8+可以设置padding。
radio/checkbox无内置padding

按钮的padding最难控制
火狐浏览器下padding为0时，左右依然有padding。设置button::-moz-focus-inner{padding:0}即可解决。IE7下，按钮文字越多，左右padding值越大，设置overflow:visible解决。
padding与高度计算不兼容。行高为20，padding为10时，IE7高度为45，IE8和谷歌浏览器高度为40，火狐为42。利用a标签替换button，或者借助label。

### 图形绘制
实现三道杠

白眼效果

background-clip:content-box实现背景色只在内容区域现实

### 布局实现
百分比单位构建固定比例布局的结构
配合margin实现等高布局
两栏自适应布局

## z-index
支持负值，支持长css3 animation动画，css2.1时需要和定位元素配合使用。

### 与css定位属性
没有嵌套：后来居上，谁大谁上。
嵌套：祖先优先原则。
注:z-index为auto时，当前层叠上下文的生成盒子层叠水平是0，盒子（除非是根元素）不会创建一个新的层叠上下文。

### 层叠上下文和层叠水平

层叠上下文表示谁离皇帝更近，层叠水平决定谁离官员更近

**层叠上下文**：

* 概念：
    三维概念，表示普通元素高人一等了。
* 存在：
    页面根元素天生有层叠上下文。
    z-index为数值的定位元素也有层叠上下文。
    设置其他属性，使得其存在。
* 特性：
    可嵌套。
    每个层叠上下文和兄弟元素独立。
    每个层叠上下文都自成体系，元素内容被层叠之后，整个元素被认为是在父层的层叠顺序中。

**层叠水平**：

* 层叠上下文中的每个元素都有一个层叠水平。
* 层叠水平就是普通元素的论资排辈
* 层叠水平和z-index不是一个东西

### 层叠顺序

* 定义：元素发生层叠时候有着特定的垂直显示顺序

* 七阶层叠水平：
装饰：
层叠上下文的background/border
负z-index，
布局：
block块状水平盒子，
float浮动盒子，
内容：
inline/inline-block水平盒子，
z-index:auto或看成z-index:0，
正z-index

    ![七阶层叠水平]()
    
* 意义：规范元素重叠时候的呈现规则

* 为什么有七阶层叠水平？
符合页面加载和页面呈现。内容最重要。

TODO 这里应该有个例子

### z-index与层叠上下文

* 定位元素默认z-index：auto可以看成z-index：0
* z-index不为auto的定位元素会创建层叠上下文
* z-index层叠顺序的比较止步于父级层叠上下文

为何定位元素会覆盖普通元素？

