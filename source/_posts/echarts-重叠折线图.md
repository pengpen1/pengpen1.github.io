---
title: 重叠折线图
date: 2024-06-12 13:55:00
tags: echarts
categories: echarts
description: 本篇文章让我们一起来看看重叠折线图如何实现的吧。阅读时长：6min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/music-3510326_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将学习竖向共用x轴和横向共用y轴这两种折线图的实现思路。

### 基础知识

**图表布局和类型**

ECharts 中`grid` 组件用于指定图表的网格（布局），而 `xAxis` 和 `yAxis` 是与 `grid` 相关联的，它们定义了图表的坐标轴。通过 `gridIndex` 属性，`xAxis` 和 `yAxis` 可以与特定的 `grid` 相关联，从而在同一个图表实例中创建多个坐标系。

`series`，它确实是用来决定图形的展示方式（如折线图、柱状图、饼图等），以及展示的数据内容。`series` 通过指定 `xAxisIndex` 和 `yAxisIndex` 来关联特定的 `xAxis` 和 `yAxis`。这样，即使你有多个 `grid`，你也可以通过指定系列与哪个 `xAxis` 和 `yAxis` 相关联，来控制该系列应显示在哪个网格中。总结：

- Grid：定义了图表的布局和边框，你可以在一个 ECharts 实例中设置多个 `grid`。
- xAxis 和 yAxis：定义了坐标轴，通过 `gridIndex` 可以关联到特定的 `grid`。
- Series：定义了图表的数据和图形类型（如柱状、折线等），通过 `xAxisIndex` 和 `yAxisIndex` 与特定的坐标轴关联，从而确定其在布局中的位置。



### 竖向共用x轴

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240612144431.png)

实现思路是先用grid对两组x轴y轴进行布局，然后隐藏掉下面一组的x轴坐标和标签，调整上面一组的x轴标签的距离即可：

```js
  const option = {
    tooltip: {
      trigger: "axis",
    },
    legend: {
      show: false,
    },
    grid: [
      {
        // Top grid
        top: "5%",
        left: "10%",
        right: "10%",
        height: "35%",
      },
      {
        // 底部折线图的grid
        left: "10%",
        right: "10%",
        top: "55%",
        height: "35%",
      },
    ],
    xAxis: [
      {
        // Top X Axis
        type: "category",
        boundaryGap: false,
        axisTick: {
          show: false, // 取消刻度线显示
        },
        axisLabel: {
          show: true, // 显示底部的名称
          margin: 12, // 标签与轴线的距离
        },
        axisLine: {
          show: false,
        },
        data: ["04-15", "04-30", "05-15", "05-30", "06-15", "06-30"],
        gridIndex: 0,
        axisPointer: {
          show: true,
        },
      },
      {
        // Bottom X Axis
        type: "category",
        show: false,
        boundaryGap: false,
        data: ["04-15", "04-30", "05-15", "05-30", "06-15", "06-30"],
        gridIndex: 1,
        axisPointer: {
          show: true,
        },
      },
    ],
    yAxis: [
      {
        // Top Y Axis
        type: "value",
        gridIndex: 0,
        splitLine: {
          show: true, // 显示网格的分隔线
          lineStyle: {
            type: "dashed", // 虚线样式
            color: "#333", // 线的颜色
          },
        },
        axisLabel: {
          color: "#cecdcfcc",
        },
      },
      {
        // Bottom Y Axis
        type: "value",
        gridIndex: 1,
        splitLine: {
          show: true, // 显示网格的分隔线
          lineStyle: {
            type: "dashed", // 虚线样式
            color: "#333", // 线的颜色
          },
        },
        axisLabel: {
          color: "#cecdcfcc",
        },
      },
    ],
    series: [
      {
        // Series for General Assets
        name: "一般资产",
        type: "line",
        xAxisIndex: 0,
        yAxisIndex: 0,
        data: [2, 6, 9, 10, 13, 16],
      },
      {
        // Series for Important Assets
        name: "重要资产",
        type: "line",
        xAxisIndex: 0,
        yAxisIndex: 0,
        data: [1, 2, 5, 8, 12, 15],
      },
      {
        // Series for Core Assets
        name: "核心资产",
        type: "line",
        xAxisIndex: 1,
        yAxisIndex: 1,
        data: [50, 100, 150, 130, 160, 190],
      },
    ],
  };
```





### 横向共用y轴

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240612145209.png)

这个的实现思路和上面一个略有不同，这次额外加了个grid和y轴，用这个新加的y轴作为左右两个折线图的共用y轴：

```js
  const option = {
    baseOption: {
      timeline: {
        show: false,
        top: 0,
        data: [],
      },
      label: {
        show: false,
      },
      tooltip: {
        trigger: "item",
        backgroundColor: "rgba(1, 13, 19, 0.5)",
        textStyle: {
          color: "rgba(255, 255, 255, 1)",
        },
      },
      grid: [
        {
          left: "5%",
          top: "10%",
          bottom: "8%",
          containLabel: true,
          width: "37%",
        },
        {
          left: "54%",
          top: "10%",
          bottom: "8%",
          width: "0%",
        },
        {
          right: "2%",
          top: "10%",
          bottom: "8%",
          containLabel: true,
          width: "37%",
        },
      ],
      xAxis: [
        {
          type: "value",
          inverse: true,
          axisLine: {
            show: false,
          },
          axisTick: {
            show: false,
          },
          position: "bottom",
          axisLabel: {
            show: true,
            color: textColor,
          },
          splitLine: {
            show: true,
            lineStyle: {
              color: lineColor,
            },
          },
        },
        {
          gridIndex: 1,
          show: false,
        },
        {
          gridIndex: 2,
          axisLine: {
            show: false,
          },
          axisTick: {
            show: false,
          },
          position: "bottom",
          axisLabel: {
            show: true,
            color: textColor,
          },
          splitLine: {
            show: true,
            lineStyle: {
              color: lineColor,
            },
          },
        },
      ],
      yAxis: [
        {
          type: "category",
          // inverse: true,
          position: "right",
          axisLine: {
            show: true,
            lineStyle: {
              color: lineColor,
            },
          },

          axisTick: {
            show: false,
          },
          axisLabel: {
            show: false,
          },
          data: xData,
        },
        {
          gridIndex: 1,
          type: "category",
          inverse: true,
          position: "left",
          axisLine: {
            show: false,
          },
          axisTick: {
            show: false,
          },
          axisLabel: {
            show: true,
            padding: [30, 0, 0, 0],
            textStyle: {
              color: "#ffffff",
              fontSize: 14,
            },
            align: "center",
          },
          data: xData.map(function (value) {
            return {
              value: value,
              textStyle: {
                align: "center",
              },
            };
          }),
        },
        {
          gridIndex: 2,
          type: "category",
          // inverse: true,
          position: "left",
          axisLine: {
            show: true,
            lineStyle: {
              color: lineColor,
            },
          },
          axisTick: {
            show: false,
          },
          axisLabel: {
            show: false,
          },
          data: xData,
        },
      ],
      series: [
        {
          name: "2018",
          type: "line",
          barWidth: 25,
          stack: "1",
          itemStyle: {
            normal: {
              color: new echarts.graphic.LinearGradient(0, 0, 1, 0, [
                {
                  offset: 0,
                  color: colors[0].start,
                },
                {
                  offset: 1,
                  color: colors[0].end,
                },
              ]),
            },
          },
          data: lastYearData,
          animationEasing: "elasticOut",
        },
        {
          name: "2018",
          type: "line",
          stack: "2",
          barWidth: 25,
          xAxisIndex: 2,
          yAxisIndex: 2,
          itemStyle: {
            normal: {
              color: new echarts.graphic.LinearGradient(0, 0, 1, 0, [
                {
                  offset: 0,
                  color: colors[1].start,
                },
                {
                  offset: 1,
                  color: colors[1].end,
                },
              ]),
            },
          },
          data: thisYearData,
          animationEasing: "elasticOut",
        },
      ],
    },
  };
```

