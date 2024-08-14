---
title: BFC解决弹性盒子宽度响应式问题
date: 2024-08-6 11:24:00
tags: css
categories: css
description: 探讨在使用弹性盒子时出现的宽度响应式问题。阅读时长：3min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/atlas-1052011_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**我们在用弹性盒子布局的时候，可能会遇到宽度并不响应的问题，本篇文章就是探讨这个问题的解决方案。

### 问题描述

下面是初始状态，下面黑色区域是对照组

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240806111146.png)

当我折叠并打开一次侧边菜单后，子项目2，出现了意外变化，宽度并没有响应式更新：
![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240806111416.png)

 ![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240806111534.png)

```css
.content {
  display: flex;
  width: 100%; /* 确保父容器宽度为100% */
}

.system-wrap {
  width: 360px;
  min-width: 360px; /* 保持最小宽度 */
  max-width: 360px; /* 保持最大宽度 */
}

.pagetable-wrap {
  flex: 1;
  margin-left: 12px;
  transition: width 0.3s; /* 添加过渡效果，确保宽度变化时有平滑过渡 */
}

```

上面是原始css代码，这代码用在对照组上面是正常响应式的，所以问题应该出在子项目2里面的内容里，但是并未找出问题所在，猜测是浏览器没正确刷新布局的原因。



### 解决方案

给子项目2，上述代码中的`.pagetable-wrap`增加`overflow: hidden`**触发触发 BFC（块级格式化上下文）**就可以解决宽度没有响应式更新的问题了，触发 BFC，这意味着该元素将创建一个独立的布局区域，其内部的元素布局不会影响外部的元素和布局。



### 结论

利用BFC我们可以解决一些意料之外的宽度响应式更新问题。


