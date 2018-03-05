---
title: css深入学习之padding
date: 2018-03-02 09:44:26
tags: css padding
header-img: "head.jpg"
---
# css深入学习之padding

## padding

### 与容器尺寸
块状元素下
padding值暴走，一定会影响尺寸。
width为非auto，padding影响尺寸
width为auto，或box-sizing为border-box时，padding 没有暴走，不影响尺寸。

内敛元素下
垂直方向的padding不会影响尺寸，水平方向会。但是会影响背景色（占据空间）

用途：分割线的实现

<iframe height='265' scrolling='no' title='gvJoxd' src='//codepen.io/chenjp/embed/gvJoxd/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/gvJoxd/'>gvJoxd</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### padding取值
不支持负值

百分比值相对于宽度计算
用途：实现所有屏幕下都是正方形

<iframe height='265' scrolling='no' title='bLyaLy' src='//codepen.io/chenjp/embed/bLyaLy/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/bLyaLy/'>bLyaLy</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

内敛元素的padding同样相对于宽度计算，默认的高宽有细节差异，会换行。
宽高差异是因为内敛元素的垂直padding会让‘幽灵空白节点’（内敛元素文本的control-area区域）显现，也就是规范中的strut出现。当font-size为零时，就好了。

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
实现三道杠，白眼效果

<p data-height="265" data-theme-id="light" data-slug-hash="aqrqgX" data-default-tab="css,result" data-user="chenjp" data-embed-version="2" data-pen-title="padding与图形绘制1" class="codepen">See the Pen <a href="https://codepen.io/chenjp/pen/aqrqgX/">padding与图形绘制1</a> by chenjp (<a href="https://codepen.io/chenjp">@chenjp</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

background-clip:content-box实现背景色只在内容区域现实

### 布局实现
百分比单位构建固定比例布局的结构，例如上面的所有屏幕一比一
配合margin实现等高布局

<iframe height='265' scrolling='no' title='padding与布局1' src='//codepen.io/chenjp/embed/PQvRjj/?height=265&theme-id=light&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/PQvRjj/'>padding与布局1</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

两栏自适应布局

<p data-height="265" data-theme-id="light" data-slug-hash="bLyMBO" data-default-tab="html,result" data-user="chenjp" data-embed-version="2" data-pen-title="padding与布局2" class="codepen">See the Pen <a href="https://codepen.io/chenjp/pen/bLyMBO/">padding与布局2</a> by chenjp (<a href="https://codepen.io/chenjp">@chenjp</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>