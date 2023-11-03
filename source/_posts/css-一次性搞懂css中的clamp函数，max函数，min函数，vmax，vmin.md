---
title: 一次性搞懂css中的clamp函数，max函数，min函数，vmax，vmin
date: 2023-09-7 17:00:00
tags: css
categories: css
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/man-8106958_1280.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**本篇文章我们将学习css中的clamp函数，max函数，min函数，vmax，vmin。

## clamp函数

2020年4月开始支持这些函数，我们来看MDN上对clamp函数的描述：是将一个值限制在一个上限和下限之间，当这个值超过最大或最小范围时，会在这两个值中选一个使用，它接收三个参数：最小值，首选值，最大值。表达式可以是数学函数 (参看 calc )、字面量或其它计算为有效的参数类型表达式，如 attr()，或嵌套的 min 和 max 。表达式中的每一个值都可以用不同的单位。

### 案例

```css
font-size: clamp(1rem, 2.5vw, 2rem);
```

上述案例设置随窗口大小改变的字体大小，但是无论窗口怎么改变，字体大小不会小于设置的最小值，也不会超过设置的最大值。

### **和百分比，媒体查询区别**

媒体查询形成流体尺寸，百分比没法最大值和最小值

clamp函数既能形成流体尺寸，也能有最大值最小值

## min和max

**min()** CSS 方法允许你从逗号分隔符表达式中选择一个最小值作为 CSS 的属性值。

```css
width: min(10vw, 4em, 80px);
```

在上面的例子中，宽度最多是 80px。如果视口的宽度小于 800px，或者一个 em 的宽度小于 20px，则会更窄。换句话说，最大宽度是 80px。同理**max()** 这个 CSS 函数让你可以从一个逗号分隔的表达式列表中选择最大（正方向）的值作为属性的值 。

## **vmax和vmin**

vmax，vmin是相对长度单位**,**vmin视口高度 vw和宽度 `vh` 两者之间的最小值。vmax视口高度 `vw` 和宽度 `vh` 两者之间的最大值。用途还是多，如手机屏幕翻转，每次都用vw或vh中的最小值，这样就算屏幕翻转也能最大效果的呈现画面，可以参考哔站手机版。


