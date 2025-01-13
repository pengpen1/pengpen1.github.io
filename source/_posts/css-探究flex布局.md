---
title: 探究flex布局
date: 2025-01-13 09:15:00
tags: css
categories: css
description: 记录我在用flex就行布局时遇到的问题，以及如何解决的。阅读时长：3min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/martin-bennie-2Q0afyHFvao-unsplash.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**为了兼容不同的宽度，我很多时候都没有固定宽度，而是用的flex来自动计算宽度，但是我遇到个问题，就是在改变屏幕宽度后再改回去，宽度没变回原样。虽然我知道用BFC可以解决这个问题，但是不知道具体是什么原因造成的，所以专门研究了下这个问题，并做了此记录。



### **展示问题**

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20250113105412.png)

可以明显看到有线框图的`.right-main-middle`要比下方的`.right-main-bottom`要高出好多，但实际上两个均为设置高度，都是用的`flex=1`来自动计算高度的。



### **解决方案**

在开始的时候我也说了，用BFC让dmo重新计算高度就行（子项目都得加上），或者给个合适的最小高度也能解决问题

```css
    overflow: hidden; // BFC
    // display: inline-block; // BFC
    // min-height: 354px; // 最小高度
```



### 探究历程

为了弄清楚原因，我们就绕不开`flex=1`的原理，我所知的，它就是`flex-grow`、`flex-shrink`、`flex-basis`三个属性的缩小，打开控制台可以查看到详情：

```css
    flex-grow: 1;
    flex-shrink: 1;
    flex-basis: 0%; // 初始大小为 0,显式地说明弹性盒子可用抢占所有空间，并按比例进行分配
```

这几个 flex 属性的作用其实就是改变了[flex](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_flexible_box_layout/Basic_concepts_of_flexbox) 容器中的可用空间的行为。同时，可用空间对于 flex 元素的对齐行为也是很重要的。

**flex-basis【该元素未伸张和收缩之前，它的大小是多少？】** 指定了 flex 元素在主轴方向上的初始大小，当一个元素同时被设置了 `flex-basis` (除值为 `auto` 外) 和 `width` (或者在 `flex-direction: column` 情况下设置了`height`) , `flex-basis` 具有*更高的优先级*，默认为 `auto`（这就解释了：我们给只要给 Flex 元素的父元素声明 `display: flex`，所有子元素就会排成一行，且自动分配大小以充分展示元素的内容）

1. `flex-basis` 设置为 `auto`，且元素设置了宽度吗？如果设置了，元素的大小将会基于设置的宽度。
2. `flex-basis` 设为了 `auto` 或 `content`（没设置宽度）? 如果设置了，元素的大小为`max-content` 。
3. `flex-basis` 是不为 `0` 的长度单位吗？如果是这样，那这就是元素的大小。
4. `flex-basis` 设为了 `0`？如果是这样，则元素的大小不在空间分配计算的考虑之内。



**flex-shrink【该元素要消除（收缩）多少负可用空间？】** 属性指定了 flex 元素的收缩规则。flex 元素仅在默认宽度之和大于容器的时候才会发生收缩，其收缩的大小是依据 flex-shrink 的值，默认为` 1`，更大的数值可以比赋予小数值的同级元素收缩程度更大。在计算 flex 元素收缩的大小时，它的最小尺寸也会被考虑进去（负可用空间消除期间弹性盒子会阻止小的元素缩小到 0，它们绝不会小于 `min-content` 的大小），[详细算法说明](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_flexible_box_layout/Controlling_ratios_of_flex_items_along_the_main_axis)

**flex-grow【该元素获得（伸张）多少正可用空间？】** 设置 flex 项 主尺寸 的 flex 增长系数，默认为 0。



有点懵对不对，看两个例子消化下

1.`flex: 1 1 auto` ：如果子元素都这样设置，那么在可用空间的分配上，是平均的，但是比较大的元素最终会变得更大，因为它一开始就有一个比较大的尺寸，即使它与其他元素具有相同的分配空间。`flex-basis` 的值为 `auto` 且没有设置它们的宽，因此它们是自动调整大小的。

2.`flex: 1 1 0`：如果子元素都这样设置，由于`flex-basis` 的值为 0，那么所有空间都用来争夺，并且所有元素具有相同的 `flex-grow` 值，它们每个都获得相等的空间分配。最终结果是三个等宽的可伸缩元素。



而`BFC`，众所周知，是**区块格式化上下文**（Block Formatting Context，BFC）是 Web 页面的可视 CSS 渲染的一部分，是块级盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。简单来说就是隔离的独立容器，容器里面的子元素不会影响到外面的元素，有很多方式都能创建BFC，详细请看[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_display/Block_formatting_context)，我们常用的基本就overflow 值不为 visible 或 clip 的块级元素、position 值为 absolute 或 fixed 的元素、display 值为 inline-block 的元素、弹性元素（display 值为 flex 或 inline-flex 元素的直接子元素），如果它们**本身既不是弹性、网格也不是表格容器**。

BFC的作用：

- 可以阻止元素被浮动元素覆盖
- 如果元素里面包含浮动元素，可以清除内部浮动（原理：父元素创建了BFC后，里面的子元素即使是float也会参与高度计算）
- 分属于不同BFC时，可以防止margin重叠，边距为两者之和



所以是因为将内部元素给隔离了，不影响到外面的元素，而解决对这个问题？还是说就是重新计算了高度解决的这个问题？来吧实验开始：

```vue
<template>
  <div class="business-hall-monitor">
    <div class="top">
      <!-- <div class="top-left"></div>
      <div class="top-right"></div> -->
    </div>
    <div class="bottom">
      <!-- <div class="bottom-left"></div>
      <div class="bottom-right"></div> -->
    </div>
  </div>
</template>

<script setup></script>

<style lang="scss" scoped>
.business-hall-monitor {
  display: flex;
  flex-direction: column;
  gap: 20px;
  height: 100vh;
  width: 100vw;
  .top,
  .bottom {
    flex: 1;
    background-color: #e7b4b4;
  }
  .bottom {
    background-color: #56e6c2;
  }
}
</style>
```

这样做无论怎么变化屏幕高度，`top`、`bottom`都是平分了父容器的高度的

```vue
<template>
  <div class="business-hall-monitor">
    <div class="top" style="height: 600px">
      <div class="top-left">
        <div style="height: 100px; background-color: #d53700"></div>
      </div>
      <div class="top-right"></div>
    </div>
    <div class="bottom">
      <div class="bottom-left"></div>
      <div class="bottom-right"></div>
    </div>
  </div>
</template>

<script setup></script>

<style lang="scss" scoped>
.business-hall-monitor {
  display: flex;
  flex-direction: column;
  gap: 20px;
  height: 100vh;
  width: 100vw;
  .top,
  .bottom {
    flex: 1;
    background-color: #e7b4b4;

    display: flex;
  }
  .bottom {
    background-color: #56e6c2;
  }

  .top-left,
  .top-right,
  .bottom-left,
  .bottom-right {
    flex: 1;
    background-color: #e7b4b4;
  }
  .top-right,
  .bottom-left {
    background-color: #56e6c2;
  }
}
</style>

<style>
.hide-layout-main {
  padding: 0px !important;
}
</style>
```

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20250113144610.png)

这样做，`top`、`bottom`也是平分了父容器的高度的，这样验证了 `flex-basis` 具有*更高的优先级*是正确的，但是我们将`style="height: 600px"`移给`top-left`事情就变得不一样了，被撑开了

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20250113144641.png)

所以出现我展示那个问题的原因就是：线框组件的高度大于了父盒子被分配到的高度，导致父盒子高度被撑高，要验证也很简单，删掉线框组件，看高度是否回归正常即可验证。

所以原因就是子项目里面的元素因为某些原因，高度超过了子项目被分配的高度，将其撑高。而用BFC创建隔离容器，刚好不让这些意外高度的元素不影响到外面的布局。

除了我这个线框图会有这个问题，我在使用echarts也遇到过一样的问题，也是开始是正常的，变化下屏幕高度也是正常的，再变回去，嘿！不正常了

而且我是写了resize事件的：

```js
const resize = () => {
  console.log("resize");
  nextTick(() => {
    // 打印容器高度
    console.log(chartRef.value.offsetHeight);
    chart && chart.resize();
  });
};

 window.addEventListener("resize", resize);
```

从打印结果来看在【再变回去】阶段打印出来的值和【变化下屏幕高度】打印出来的值是一样的，也就是说，容器高度在屏幕变化时并没有正确被计算，所以我们需要BFC来解决这个问题（创建隔离容器，正确计算高度）。

```css
  .right-main-middle {
    display: flex;
    flex: 1;
    gap: 20px;
    overflow: hidden; // BFC
    // display: inline-block;
    // min-height: 354px;
  }

  .right-main-bottom {
    display: flex;
    flex: 1;
    gap: 20px;
    // min-height: 354px;
    overflow: hidden;
  }
```



那有爱提问的小明要问了，`min-height`为什么也能解决这个问题？因为`min-height` 是最小尺寸的约束，`flex-basis` 计算出的值小于 `min-height`，则 `min-height` 会生效




