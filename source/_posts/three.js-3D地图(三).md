---
title: three.js制作3D地图(三)
date: 2024-05-30 11:26:00
tags: three.js
categories: three.js
description: 上期我们成功的完成了需求，画出了3D地图，这期优化一下进场动画。阅读时长：18min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240326144804.gif
top_img: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240326144804.gif
---
**概要：**在这篇记录中，我们将学习关于Three.js以及进场动画等相关方面的知识。

### 当前实现

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240524160145.png)

当前是一开始都渲染，设置了两个时间点bgDuration和opacityDuration，发光效果由UnrealBloomPass实现，具体如下图所示：

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240524165106.png)



### 理清需求

1.最开始的时候只有一层地图的边线，多条边线在其上方开始出现，并且颜色从浅白变天蓝色（并附带泛光光效果），然后颜色再变成浅蓝色（泛光效果减弱）

2.完成第一步后，各个省开始从透明状态过渡到不透明状态，相机位置向右上方移动，或者地图向左下移动，实现地图向屏幕中间移动的感觉



### 实现需求

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240524160151.png)

先去掉标签和以上报省份的顶部描边（浅蓝色部分），这个直接改透明度即可。

标签和标记改成在opacityDuration后依次出现，用定时器即可。

```js
  const showAllMarkPoints = (isTransition = true) => {
    const markPoints = Array.from(finallyConfigMap.values()).filter(
      (item) => item.mark
    );

    markPoints.forEach((item, index) => {
      if (isTransition) {
        setTimeout(() => {
          item.mark.show();
        }, 100 * index);
      } else {
        item.mark.show();
      }
    });
  };
```



