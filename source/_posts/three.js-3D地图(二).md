---
title: three.js制作3D地图(二)
date: 2024-05-22 10:46:00
tags: three.js
categories: three.js
description: 上期我们成功的完成了需求，画出了3D地图，这期优化一下进场动画。阅读时长：18min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240326144804.gif
top_img: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240326144804.gif
---
**概要：**在这篇记录中，我们将学习关于Three.js以及进场动画等相关方面的知识。

### 基础知识

如您所见，本篇文章的封面就是公司UI设计的动画效果图，看上去很难，其实一点都不简单。我们需要满足视角适配、标记省份、追光效果、背景呼吸效果。先来了解一些准备工作和基本知识吧



### 疑问解答

**1.Polygon 和 MultiPolygon各是什么？**

- 在地理信息系统（GIS）中，Polygon 和 MultiPolygon 是用于描述地理空间多边形的两种几何类型。
- Polygon（多边形）：Polygon 是一种简单的几何类型，用于表示封闭的二维区域，通常由一组有序的点（顶点）组成，最后一个点会自动连接到第一个点，形成一个封闭的区域。
  例如，一个国家的边界、一个湖泊的边界或者一个建筑物的外轮廓都可以用 Polygon 来描述。
- MultiPolygon（多重多边形）：MultiPolygon 是一种复杂的几何类型，用于表示多个独立的、不相交的多边形组成的集合。每个 MultiPolygon 对象包含多个 Polygon 对象，每个 Polygon 表示一个独立的封闭区域。例如，一个国家可能由多个岛屿组成，每个岛屿的边界都可以用一个 Polygon 来描述，而这些岛屿的集合则可以用一个 MultiPolygon 来表示。



### 参考链接

- [用Three.js搞个炫酷的3D区块地图 - 掘金 (juejin.cn)](https://juejin.cn/post/7250375753598844983)

