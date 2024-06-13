---
title: 封装电池组件
date: 2024-06-13 16:20:00
tags: vue
categories: vue
description: 本篇文章让我们一起来看看如何实现电池组件的封装。阅读时长：6min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/reading-3723751_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将学习实现电池组件的思路。

### 思路1：计算宽度实现

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240613163711.png)

这个方案简单粗暴，外层容器加个溢出隐藏，内部电量的宽度根据传入的电量直接计算即可，可以精确到小数。

```vue
<template>
  <div class="battery">
    <div
      class="battery-level"
      :style="{ width: filledCellsWidth, backgroundColor: color }"
    ></div>
  </div>
</template>

<script setup>
import { computed } from "vue";

const props = defineProps({
  percentage: {
    type: String,
    required: true,
  },
  color: {
    type: String,
    required: true,
  },
});
// 计算填充宽度
const filledCellsWidth = computed(() => `${props.percentage}%`);
</script>

<style scoped>
.battery {
  width: 200px;
  height: 50px;
  border: 1px solid #ccc;
  border-radius: 8px;
  background-color: #e0e0e0;
  position: relative;
  overflow: hidden;
}

.battery-level {
  height: 100%;
  transition: width 0.3s ease;
}
</style>
```



### 思路2：计算电量格数实现

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240613164104.png)

实现思路是固定电量格子数，然后根据传入的电量，计算出占多少个电量格子（向上取整），然后渲染出来即可，这个发案不能精确到小数。

```js
<template>
  <div class="battery">
    <div
      class="cell"
      v-for="cell in cells"
      :key="cell.id"
      :style="cell.style"
    ></div>
  </div>
</template>

<script setup>
import { computed } from "vue";

const props = defineProps({
  percentage: {
    type: Number,
    required: true,
  },
  color: {
    type: String,
    required: true,
  },
});

const totalCells = 10; // 总电池格数

// 创建电池格数组
const cells = computed(() => {
  const cellsFilled = Math.ceil((props.percentage / 100) * totalCells);
  return Array.from({ length: totalCells }, (_, index) => ({
    id: index,
    style: {
      backgroundColor: index < cellsFilled ? props.color : "#e0e0e0",
      borderTopLeftRadius: index === 0 ? "50% 50%" : 0,
      borderBottomLeftRadius: index === 0 ? "50% 50%" : 0,
      borderTopRightRadius: index === totalCells - 1 ? "50% 50%" : 0,
      borderBottomRightRadius: index === totalCells - 1 ? "50% 50%" : 0,
    },
  }));
});
</script>

<style scoped>
.battery {
  display: flex;
  width: 260px;
  height: 80px;
  padding: 2px;
  border: 1px solid #ccc;
  border-radius: 12px;
  background-color: #e0e0e0;
}

.cell {
  flex: 1;
  margin: 2px;
  transition: background-color 0.3s ease;
}
</style>
```





### 思路3：计算遮罩宽度实现

实现思路，先把电量渲染出来，当成背景，然后根据传进来的电量，计算遮罩的宽度，然后遮罩从右向左进行遮挡即可。



### 总结

实现方式多种多样，需求也是多种多样，我们要扩展自己的思路，以应对将来的各种需求。