---
title: 扩展声明vue全局属性
date: 2023-09-6 14:40:00
tags: ts
categories: ts
cover: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**我们在使用vue3开发项目时，会发现很多地方都会用到格式化utc时间，所以我想加个全局属性方便自己使用，但是ts类型检测不通过，而本篇文章就是探讨这个问题的实现方案。

## 使用全局属性

```ts
export function registerProperties(app: App) {
  app.config.globalProperties.$filters = {
    formatDate(value: string) {
      return formatUtcString(value)
    }
  }
}
```

使用只需要：

```vue
{{ $filters.formatDate(utcString) }}
```

注册好$filters这个全局属性后，项目是可以正常运行，但是ts检测一直报错，说类型上不存在$filters这属性。于是我就看文档，搜解决办法。

## 解决方案

我们需要扩展一下vue中的模块，而vue正好暴露了一个帮助我们拓展模块ComponentCustomProperties。我们需要在src下创建.ts 或 .d.ts文件，然后在里面扩展类型ComponentCustomProperties，ts会合并其中的声明，就好像是在原始文件中声明一样。这里我是在src目录下创建的vue.d.ts文件。

```ts
// 扩展全局属性类型
declare module 'vue' {
  interface ComponentCustomProperties {
    $filters: {
      formatDate: (value: string) => nay
    }
  }
}
export {}
```

## 结论

声明完后，我们就可以在template中随意用$filters这个全局属性了，还会有代码提示！


