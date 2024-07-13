---
title: 封装悬浮组件
date: 2024-06-25 09:31:00
tags: vue
categories: vue
description: 本篇文章让我们一起来看看如何实现悬浮组件的封装。阅读时长：6min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/bible-1868070_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将学习实现悬浮组件的思路。

### 需求

我打算给最近新起的项目-thre.js演示场增加一个悬浮按钮，正常状态就半透明，然后缩小紧贴右边，鼠标悬浮上去后，变为不透明，且变大并显示完整内容，可以在其上按住鼠标左键进行上下拖动。我们来看看如何实现这个需求吧。



### 基础知识

药拖动这些，肯定少不了鼠标事件，下面是我们可能要用到的相关事件。

#### 相关事件

1. **mouseenter**： 当鼠标指针进入一个元素时触发。用于显示隐藏内容、改变样式、触发动画等。
2. **mousedown**： 当用户按下鼠标按钮时触发。用于开始拖拽操作、绘图、按住时长判断等。
3. **mousemove**： 当鼠标在元素上移动时触发。用于拖拽操作、绘图、实时跟踪鼠标位置等。
4. **mouseup**： 当用户释放鼠标按钮时触发。用于结束拖拽操作、停止绘图、判断点击完成等。
5. **mouseleave**： 当鼠标指针离开一个元素时触发。用于隐藏内容、恢复样式、停止动画等。



### 实现思路

**样式绑定**：使用`buttonStyle`计算属性动态控制按钮的样式，包括透明度、缩放、位置等。也可以根据状态动态改变class，然后根据clsss来实现不同的样式。

**鼠标事件处理**：`onMouseOver`和`onMouseLeave`控制按钮的悬浮状态；`onMouseDown`、`onMouseUp`和`onMouseMove`实现拖拽功能。

**CSS过渡效果**：通过`transition`属性来平滑过渡透明度和尺寸变化。



### 实现过程

我们先把处了拖动之外的样式和交互做出来，还有关闭按钮。在做关闭按钮时，我遇到个bug，监听点击事件不生效，我初步判断父级的mousedown拦截了这个事件。

```vue
<template>
  <div
    v-if="isShow"
    class="floating-button"
    :style="buttonStyle"
    @mouseover="onMouseOver"
    @mouseleave="onMouseLeave"
    @mousedown="onMouseDown"
    @mouseup="onMouseUp"
    @mousemove="onMouseMove"
  >
    <!-- 这里放置按钮的内容 -->
    <slot name="expand" v-if="isHovered"> 展开后显示内容 </slot>

    <!-- 尾部，关闭按钮 -->
    <div v-if="isHovered" class="floating-button-tail">
      <div class="vertical-line"></div>
      <font-awesome-icon class="close-icon" :icon="['fas', 'xmark']" @click="clickCloseHandle" />
    </div>
    <slot name="collapse" v-else> 笔记 </slot>
  </div>
</template>
```

查了下MDN，`click` 事件确实是在 [`mousedown`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/mousedown_event) 和 [`mouseup`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/mouseup_event) 事件依次触发后再触发。但是我也没在这两个事件中使用 `event.stopPropagation()` 来阻止事件冒泡呀。真是奇怪，只能是认为浏览器判定这是一次mousedown和一次mouseup事件吧。

解决这个问题的办法也多，一个是在按钮那加阻止mousedown冒泡的逻辑：

```
<font-awesome-icon class="close-icon" :icon="['fas', 'xmark']" @mousedown.stop @click="clickCloseHandle" />
```

第二种方案就是不使用click事件，而是就利用最外层元素的mousedown事件，来进行判定：

```js
const onMouseDown = (event) => {
  console.log("onMouseDown", event.target.tagName);
  // 因为点击事件会触发MouseDown，从而导致意外情况，所以在MouseDown中判断是否是点击事件
  if (event.target.tagName === "path" || event.target.tagName === "svg") {
    clickCloseHandle();
  }
  isDragging.value = true;
  startY.value = event.clientY;
  startTop.value = top.value;
};
```

延伸想法，外层套个wrap，拖动范围还是在按钮那，但是拖动是对wrap元素的Top属性生效的，这样可以带动wrap中其他元素进行移动，还能增加不同的点击事件。



### TODO


