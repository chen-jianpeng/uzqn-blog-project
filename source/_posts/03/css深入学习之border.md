---
title: css深入学习之border
date: 2018-03-01 09:44:26
tags: css
header-img: "head.jpg"
---
# css深入学习之border

## border-width不支持百分比
边框不会随着容器大小的变化而变化
类似的还有outline、box-shadow、text-shadow
关键字thin medium thick
默认值为3，因为border-style：double有效果至少要3像素

## border-style
dashed  虚线
谷歌火狐实色区域宽高3:1，实色和透明色1:1。IE实色宽高2:1，实色透明色2:1
border-emate解决此问题

dotted 点线
谷歌火狐小方，ie小圆

double 双线
计算规则：双线宽度相等，中间区域+-1
实例：实现三道杠的菜单按钮

inset 内凹outset 外凸，山脊山谷很不常用

## border-color color
border-color默认为color的颜色，border-shadow，text-shadow，outline也是
用途：hover变色，只需要在父级上添加即可

## background-position
相对于左上角定位。
实现右下角定位，因为position定位不包括border，所以设置position的x为100%，border为固定值的宽度即可。

## 三角等图形构建
IE78实现圆角
三道杠
三角实现
模拟圆角，上下两个梯形

边框原理!()[]

## 透明边框
增加相应区域大小
filter：drop-shadow改变图片颜色

## 布局使用
border等高布局，不支持百分比
margin与padding实现等高布局

以上内容来自张鑫旭老师在慕课网上的课程