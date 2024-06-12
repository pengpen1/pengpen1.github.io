---
title: 循环展示tooltip
date: 2024-06-06 11:32:00
tags: echarts
categories: echarts
description: 有时候我们需要定时展示tooltip，这里我们一起来学习下吧。阅读时长：8min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/watch-4638673_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将学习echarts中的折线图，饼图如何定时展示tooltip。

### 基础知识

**action**

ECharts 中支持多种图表行为，比如缩放、高亮、tooltip，除了我们手动触发外还能通过实例上的 [dispatchAction](https://echarts.apache.org/zh/api.html#echartsInstance.dispatchAction)函数进行触发。比如触发缩放：

```js
myChart.dispatchAction({
    type: 'dataZoom',
    start: 20,
    end: 30
});
```



**setOption**

设置图表实例的配置项以及数据的万能接口，所有参数和数据的修改都可以通过 `setOption` 完成，ECharts 会合并新的参数和数据，然后刷新图表。根据这个特质，我们可以做很多功能，如循环展示自定义文本，图片等等。



**定时器控制器**

对于一个大型可视化图表，或者大屏，可能会用到很多次定时器。我们最好是进行统一管理，免得出现内存泄露或者意料之外的情况。

比如我最近做的大屏，结构上分为三层，每一层都会用到定时器，第一层主菜单，需要定时获取当前时间进行显示。第二层为各个大屏，包含两侧图表和中间的地图，有些需要无缝滚动，有些需要定时展示tooltip。第三层为大屏公用的地图组件，需要每隔10s切换标记点。这三层如果各自分开管理，那维护的人会很难受，所以我封装了下面的定时器控制器：

```js
class TimerControl {
  constructor() {
    this.timers = [];
  }

  // 添加定时器
  addTimer(timer) {
    if (Array.isArray(timer)) {
      timer.forEach((t) => this.addTimer(t));
    } else {
      const { interval, callback, id } = timer;

      if (this.hasTimer(id)) {
        // 如果已存在id相同的定时器，则先移除
        this.removeTimer(id);
        console.log("已存在id为" + id + "的定时器，已移除!");
      }

      const timerItem = setInterval(callback, interval);
      this.timers.push({ id, timerItem, interval, callback });
    }
  }

  // 移除定时器
  removeTimer(id) {
    const index = this.timers.findIndex((timer) => timer.id === id);
    if (index !== -1) {
      clearInterval(this.timers[index].timerItem);
      this.timers.splice(index, 1);
      console.log(`定时器${id}已移除`, this.hasTimer(id), this.timers);
    }
  }

  // 根据ID查询是否存在指定定时器
  hasTimer(id) {
    return this.timers.some((timer) => timer.id === id);
  }

  // 获取所有定时器
  getAllTimers() {
    return this.timers;
  }

  // 获取所有定时器ID
  getAllTimerIds() {
    return this.timers.map((timer) => timer.id);
  }

  // 销毁所有定时器
  destroyAllTimers() {
    this.timers.forEach((timer) => {
      clearInterval(timer.timerItem);
    });
    this.timers = [];
  }
}

// 示例
const timers = [
  {
    interval: 1000,
    callback: () => console.log("定时器1执行"),
    id: 1,
  },
  {
    interval: 5000,
    callback: () => console.log("定时器2执行"),
    id: 2,
  },
];
export default new TimerControl();

```



### 实现思路

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240612100941.png)

1.对于简单图表，数据基本是不会变化的，所以，我们只需使用定时的取消之前所有高亮图形和高亮并提示当前数据的图形即可：

```js
// 定时展示 Tooltip
const showNextTooltip = (data) => {
  const dataLen = data.length; // 假设所有数据长度一致
  currentIndex = (currentIndex + 1) % dataLen;

  // 取消之前所有高亮的图形
  chart.dispatchAction({
    type: "downplay",
    seriesIndex: 0,
  });

  // 高亮当前图形
  chart.dispatchAction({
    type: "highlight",
    seriesIndex: 0,
    dataIndex: currentIndex,
  });

  chart.dispatchAction({
    type: "showTip",
    seriesIndex: 0, // 根据实际情况修改系列索引
    dataIndex: currentIndex,
  });
};
```

2.除了高亮，一般还要同时切换中间的文本，我们只需要用setOption更新下：

```js
// 更新中心文字
function updateCenterText(params) {
  if (!params) {
    return;
  }
  let name = params.name;
  if (name.length > 8) {
    name = name.slice(0, 7) + "...";
  }

  chart.setOption({
    graphic: [
      {
        id: "name",
        type: "text",
        left: "center",
        top: "45%",
        z: 10,
        silent: true,
        style: {
          text: name,
          //   font: "bold 20px Microsoft YaHei",
          fontSize: 12,
          fill: "#b2b0b0",
        },
      },
      {
        id: "value",
        type: "text",
        left: "center",
        top: "55%",
        z: 10,
        silent: true,
        style: {
          text: params.value,
          fontSize: 18,
          fill: "#ffffff",
          lineWidth: 2,
          //   font: "bold 16px Microsoft YaHei",
        },
      },
    ],
  });
}
```

3.除此之外，我们还要支持，点击不同数据时，从当前数据的dataIndex进行更新定时器，并显示对应的文本：

```js
  // 增加点击事件
  chart.on("click", function (params) {
    currentIndex = params.dataIndex;
    // 取消之前高亮的图形
    chart.dispatchAction({
      type: "downplay",
      seriesIndex: 0,
    });
    chart.dispatchAction({
      type: "highlight",
      seriesIndex: 0,
      dataIndex: currentIndex,
    });
    updateCenterText(params);

    //   重新设置定时器
    timerControl.removeTimer(props.timerId);
    timerControl.addTimer({
      interval: props.timerInterval,
      callback: () => {
        console.log(
          `图表定时器${props.timerId},间隔--> ${props.timerInterval}`
        );
        handleChange();
      },
      id: props.timerId,
    });
  });
```

4.对于数据会改变的复杂图表，我们得在加个定时器，并且这个定时器的间隔还得动态计算以及边界处理，我们才能实现循环展示完当前数据，然后切换到另一组数据的效果

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240612101205.png)

像上面的定时器，我都是放在组件内部的，内部不用关心传入的数据会不会改变。而切换数据的定时器我是放到上层，也就是页面层，因为这涉及到请求数据。

对于定时器，如果你不能保证是第一次添加，那最好在添加时移除一下(定时器控制器里面有重复ID自动删除的逻辑)。切换代码如下：

```js
  // 分类分级定时器事件
  const handleTypeChartTimer = async () => {
    // 切换tap，获取数据
    typeChartProps.activeKey =
      typeChartProps.activeKey === "type" ? "class" : "type";

    typeChartProps.value = await getTypeData(typeChartProps.activeKey);

    // 无需在这里清除组件内的循环展示定时器，因为数据变化了，组件会重新添加定时器

    // 添加新的tabs切换定时器
    timerControl.removeTimer(3);
    timerControl.addTimer({
      interval: typeChartProps.interval,
      callback: () => {
        console.log("分类分级图表定时器-> 5s");
        handleTypeChartTimer();
      },
      id: 3,
    });
  };
```

获取数据我们要动态计算时间间隔，在上面例子中就是typeChartProps.interval，代码如下：

```js
  const getTypeData = async (type) => {
    let result = null;
    if (type === "type") {
      result = [
        { value: 1048, name: "Search Engine" },
        { value: 735, name: "Direct" },
        { value: 580, name: "Email" },
        { value: 484, name: "Union Ads" },
        { value: 300, name: "Video Ads" },
      ];
    } else {
      result = [
        { value: 555, name: "风险类型" },
        { value: 333, name: "敏感分级" },
        { value: 222, name: "敏感分类" },
      ];
    }

    if (result.length > 0) {
      typeChartProps.interval = result.length * 5000;
    } else {
      // 当没数据时要给个默认时间，防止快速来回切换
      typeChartProps.interval = 5000;
    }

    return result;
  };
```

嘿，要是我想除了自动切换数据，我还能手动切换数据，又该如何做呢？只需监听切换时间，重置定时器，然后再请求一次即可，这就是关注点分离的好处，上层只关心数据的切换，不用管展示相关的逻辑。

```js
    // 分类分级点击事件
    clickHandle: async (item) => {
      if (item && item.key !== typeChartProps.activeKey) {
        typeChartProps.activeKey = item.key;

        timerControl.removeTimer(3);
        timerControl.addTimer({
          interval: typeChartProps.interval,
          callback: () => {
            handleTypeChartTimer();
          },
          id: 3,
        });

        typeChartProps.value = await getTypeData(typeChartProps.activeKey);
      }
    },
```

5.总结，页面层注重数据，只需要定时切换数据即可。组件层只关心传入的数据，并循环展示即可。



### 组件完整代码

这是我封装的饼图组件，支持tab切换，支持自动循环展示tip，支持手动查看当前数据，LargeScreenCard是容器封装，这根据业务需要自己封装即可。

```vue
<template>
  <LargeScreenCard v-bind="cardProps">
    <div class="tabs-wrapper" v-if="enableTabs">
      <div
        v-for="(item, index) in tabs"
        @click="clickHandle(item)"
        :key="index"
        :class="activeKey === item.key ? 'tab-item-active' : 'tab-item'"
      >
        {{ item.name }}
      </div>
    </div>
    <div v-if="isPlaceholder" class="chart-placeholder">{{ placeholder }}</div>
    <div v-else style="width: 100%; height: 100%">
      <div class="chart-wrapper" ref="chartRef"></div>
    </div>
  </LargeScreenCard>
</template>

<script setup>
import { onMounted } from "vue";
import LargeScreenCard from "@/components/views/PIMSYw/LargeScreenMap/Common/Card/LargeScreenCard";
import timerControl from "@/services/views/PIMSYw/LargeScreenMap/Utils/timerControl";
import * as echarts from "echarts";
import { defineProps, watch, nextTick, ref, computed } from "vue";
import { onBeforeUnmount } from "vue";
const props = defineProps({
  value: {
    type: Array,
    default: () => [],
  },
  cardProps: {
    // LargeScreenCard的配置
    type: Object,
    default: () => {
      return {};
    },
  },
  enableTabs: {
    // 是否开启标签页切换，优先级高于Tabs,activeName
    type: Boolean,
    default: () => {
      return false;
    },
  },
  tabs: {
    type: Array,
    default: () => [],
  },
  // 当前激活项
  activeKey: String,
  clickHandle: {
    // 切换tab回调
    type: Function,
    default: () => {
      return () => {};
    },
  },
  timerId: {
    // 定时器id,如果一个页面用到多次本组件，则需要传入不同的timerId!
    type: Number,
    default: () => {
      return 777;
    },
  },
  timerInterval: {
    // 定时器循环间隔，单位是毫秒
    type: Number,
    default: () => {
      return 5000;
    },
  },
  placeholder: {
    type: String,
    default: () => {
      return "暂无数据";
    },
  },
});

const chartRef = ref(null);
let chart = null;
let currentIndex = 0;

const isPlaceholder = computed(() => {
  return !(props.value && props.value.length);
});

const resize = () => {
  chart && chart.resize();
};
const init = (value) => {
  const chartDom = chartRef.value;
  if (!chartDom) return;

  currentIndex = 0;
  chart = echarts.getInstanceByDom(chartDom);
  if (!chart) {
    chart = echarts.init(chartDom);
  }
  value.forEach((item, i) => (item.label = { color: "#fff" }));

  let center = ["50%", "50%"];
  if (props.enableTabs) {
    center = ["50%", "55%"];
  }

  const option = {
    tooltip: {
      trigger: "item",
      backgroundColor: "rgba(1, 13, 19, 0.5)",
      textStyle: {
        color: "rgba(255, 255, 255, 1)",
      },
    },
    color: [
      "#44CE30 ",
      "#30CE7D ",
      "#30CEBC",
      "#30A6CE",
      "#307FCE",
      "#3043CE",
      "#3C30CE",
      "#6330CE",
    ],
    series: [
      {
        name: "pie",
        type: "pie",
        center,
        radius: ["50%", "62%"],
        labelLine: {
          show: true,
          lineStyle: {
            color: "#A1A1A1",
          },
        },
        label: {
          show: true,
          position: "outside",
          formatter: function (params) {
            if (params.name !== "") {
              return (
                "{percent|" +
                params.percent +
                "%" +
                "}" +
                "\n" +
                "{name|" +
                params.name +
                "}"
              );
            } else {
              return "";
            }
          },
          rich: {
            // 富文本
            name: {
              fontSize: 12,
              color: "#b2b0b0",
              padding: [0, 5],
              align: "left",
            },
            percent: {
              fontSize: 14,
              align: "left",
              padding: [20, 5, 8, 5],
            },
          },
        },
        itemStyle: {
          //间隔
          borderWidth: 4,
          borderColor: "#050e31",
        },
        data: value,
      },
    ],
  };
  chart.setOption(option);

  // 初始化显示图形、高亮
  updateCenterText(value[0]);
  chart.dispatchAction({
    type: "highlight",
    seriesIndex: 0,
    dataIndex: value.length ? 0 : -1,
  });

  // 增加点击事件
  chart.on("click", function (params) {
    currentIndex = params.dataIndex;
    // 取消之前高亮的图形
    chart.dispatchAction({
      type: "downplay",
      seriesIndex: 0,
    });
    chart.dispatchAction({
      type: "highlight",
      seriesIndex: 0,
      dataIndex: currentIndex,
    });
    updateCenterText(params);

    //   重新设置定时器
    timerControl.removeTimer(props.timerId);
    timerControl.addTimer({
      interval: props.timerInterval,
      callback: () => {
        console.log(
          `图表定时器${props.timerId},间隔--> ${props.timerInterval}`
        );
        handleChange();
      },
      id: props.timerId,
    });
  });

  timerControl.addTimer({
    interval: props.timerInterval,
    callback: () => {
      console.log(
        `图表定时器${props.timerId},间隔-->  ${props.timerInterval}`,
        value,
        chart
      );
      handleChange();
    },
    id: props.timerId,
  });

  window.removeEventListener("resize", resize);
  window.addEventListener("resize", resize);
};

// 更新中心文字
function updateCenterText(params) {
  if (!params) {
    return;
  }
  let name = params.name;
  if (name.length > 8) {
    name = name.slice(0, 7) + "...";
  }

  chart.setOption({
    graphic: [
      {
        id: "name",
        type: "text",
        left: "center",
        top: "45%",
        z: 10,
        silent: true,
        style: {
          text: name,
          //   font: "bold 20px Microsoft YaHei",
          fontSize: 12,
          fill: "#b2b0b0",
        },
      },
      {
        id: "value",
        type: "text",
        left: "center",
        top: "55%",
        z: 10,
        silent: true,
        style: {
          text: params.value,
          fontSize: 18,
          fill: "#ffffff",
          lineWidth: 2,
          //   font: "bold 16px Microsoft YaHei",
        },
      },
    ],
  });
}

// 定时切换展示的告警类型
function handleChange() {
  const data = props.value;
  if (!data.length) return false;
  currentIndex = (currentIndex + 1) % data.length;
  // 取消之前所有高亮的图形
  chart.dispatchAction({
    type: "downplay",
    seriesIndex: 0,
  });
  chart.dispatchAction({
    type: "highlight",
    seriesIndex: 0,
    dataIndex: currentIndex,
  });
  //  获取合并默认值后的配置，拿到当前对应的数据
  const params = chart.getOption().series[0].data[currentIndex];
  updateCenterText(params);
}

watch(
  () => props.value,
  (value) => {
    nextTick(() => {
      init(value ?? []);
    });
  }
);

onBeforeUnmount(() => {
  window.removeEventListener("resize", resize);
});
</script>

<style lang="scss" scoped>
.chart-wrapper {
  width: 100%;
  height: 100%;
}
.chart-placeholder {
  height: 100%;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  color: #86a0b3;
}
.tabs-wrapper {
  position: absolute;
  z-index: 8;
  right: 20px;
  top: 14px;
  display: flex;
  gap: 0 10px;

  .tab-item {
    padding: 1px 5px;
    border: 1px solid rgb(109, 111, 112);
    color: rgb(109, 111, 112);
    background-color: rgb(56, 59, 59);
    font-size: 12px;
    cursor: pointer;
  }
  .tab-item-active {
    padding: 1px 5px;
    border: 1px solid rgb(93, 183, 201);
    font-size: 12px;
    color: rgb(30, 169, 224);
    cursor: pointer;
    background: linear-gradient(
      to left,
      rgba(76, 166, 255, 0.43) 0%,
      rgba(76, 166, 255, 0) 100%
    );
  }
}
</style>
```





