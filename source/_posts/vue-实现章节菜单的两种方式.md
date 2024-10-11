---
title: 实现章节菜单的的两种方式
date: 2024-10-11 09:31:00
tags: vue
categories: vue
description: 本篇文章让我们一起来看看如何实现章节菜单。阅读时长：6min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/wolfgang-hasselmann-ULfobILrTpM-unsplash.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**我们在实现自己的文章或者文档的时候，章节菜单是必不可少的，点击菜单中的章节，页面滚动到对应位置。页面滚动到章节所属位置后，对应的菜单也该高亮显示。让我们来看看怎么实现这个需求吧。



### 基础知识

1. **Window.scrollY**：返回文档在垂直方向已滚动的像素值。
2. **e.scrollTop**： 获取或设置元素内容从其顶部边缘滚动的像素数（针对容器的滚动距离，值不一定是整数）。
3. **e.offsetTop**： 返回当前元素相对于其`offsetParent`元素的顶部内边距的距离。
4. **e.offsetHeight**： 返回该元素的像素高度，高度包含该元素的垂直内边距和边框（值为整数）。
5. **e.scrollIntoView**： 滚动容器，使元素在容器中对用户可见，可以设置平滑滚动`smooth`,顺时滚动`instant`,自动计算`auto`。
6. **IntersectionObserver**： 交叉观察器，用来监听可见区域的特定变化值，可以在同一个观察者对象中配置监听多个目标元素，详细可查阅[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/IntersectionObserver)。



### 方案1：使用交叉观察器

用法相当简单明了，先配置和写出超过指定阈值后调用的回调函数，然后给需要观察的元素都观察上即可。

```js
  const options = {
    root: document.querySelector("#scrollArea.scrollbar"), // 视口
    rootMargin: "0px",
    threshold: 0.5, // 章节可见度阈值，达到50%时执行
  };

  // 方案1：使用交叉观察器
  observer.value = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      // entry是一个IntersectionObserverEntry对象的数组；entry.isIntersecting 表示目标元素是否可见
      if (entry.isIntersecting) {
        console.log("章节可见", entry);
        activeId.value = Number(
          entry.target.getAttribute("id").replace("title", "")
        ); // 更新当前激活章节
      }
    });
  }, options);
  if (props.data.length) {
    props.data.forEach((item) => {
      const e = document.querySelector(`#title${item.id}`);
      if (e) {
        observer.value.observe(e);
      }
    });
  }
```



### 方案2：使用滚动事件

监听容器的scroll事件，拿到容器的滚动距离也就是`e.scrollTop`，然后遍历需要观察的章节dom，判断滚动位置是否以达到章节的顶部以及是否未超过章节的顶部，这里也可以做些处理，让章节提前高亮。我因为用了`el-scrollbar`，就没有用`e.onscroll`，逻辑都是一样的。

```js
    <el-scrollbar
      class="scrollbar"
      id="scrollArea"
      ref="container"
      @scroll="updateScroll"
    >
    </el-scrollbar>

const updateScroll = ({ scrollTop }) => {
  notes.value.forEach((sec) => {
    // let top = content.value.scrollTop; // Window.scrollY返回文档在垂直方向已滚动的像素值 e.scrollTop
    let offset = sec.offsetTop - 150; // 返回当前元素相对于其 offsetParent 元素的顶部内边距的距离,-150使标题在接近顶部时更早地被高亮，增强用户体验
    let height = sec.offsetHeight; // 返回该元素的像素高度，高度包含该元素的垂直内边距和边框，且是一个整数
    console.log(scrollTop, offset, height);
    let id = sec.getAttribute("id").replace("wrap", "");

    if (scrollTop >= offset && scrollTop < offset + height) {
      // 判断滚动位置是否已达到章节顶部和是否还未超过章节底部
      activeId.value = Number(id);
      console.log("activeId", activeId.value);
    }
  });
};
```

