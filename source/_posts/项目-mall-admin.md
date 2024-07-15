---
title: mall-admin
date: 2024-06-27 15:14:00
categories: 项目
description: 记录一下写mall-admin的历程。阅读时长：2min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/leaves-1076307_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**私人商城项目的后台管理系统，基于vue3、element-plus、typeScript实现。



### 创建项目

项目基于[vue-admin-box](https://github.com/cmdparkour/vue-admin-box)实现



### 复习TypeScript

ts一年没用了，为了快速上手，简单复习下常用的，项目中遇到有问题的再记录学习下。

#### 基础类型

- **基本类型**：`number`, `string`, `boolean`, `null`, `undefined`
- **数组**：`number[]` 或 `Array<number>`
- **元组**：`[string, number]`
- **枚举**：`enum Color {Red, Green, Blue}`
- **any**：允许任何类型
- **void**：没有任何类型
- **object**：非原始类型



#### 接口

**定义接口**：

```ts
interface Person {
  name: string;
  age: number;
}
```

**可选属性**：

```ts
interface Person {
  name: string;
  age?: number;
}
```

**只读属性**：

```ts
interface Person {
  readonly id: number;
  name: string;
}
```



#### 类

```TS
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  move(distance: number = 0) {
    console.log(`${this.name} moved ${distance}m.`);
  }
}
```



#### 函数

**函数类型**：

```ts
function add(x: number, y: number): number {
  return x + y;
}
```

**可选参数和默认参数**：

```ts
function buildName(firstName: string, lastName?: string) {
  return `${firstName} ${lastName || ''}`;
}
```

**剩余参数**：

```ts
function buildName(firstName: string, ...restOfName: string[]) {
  return `${firstName} ${restOfName.join(' ')}`;
}
```



#### 泛型

**基本使用**：

```ts
function identity<T>(arg: T): T {
  return arg;
}
```

**泛型类**：

```ts
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}
```



#### 类型断言

```ts
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```



#### 其他

**索引签名**：

提供一种灵活的方式来描述具有动态属性的对象结构。它允许对象具有一组已知的属性，同时还能扩展出更多不预先定义的属性，满足实际应用中的需求。

```TS
export interface Style {
  default: Colors;
  light: Colors;
  dark: Colors;
  [propName: string]: Colors;
}
```

**联合类型**：

```ts
let value: string | number;
value = "hello";
value = 123;
```

**类型别名**：

```ts
type Name = string;
type NameOrResolver = Name | (() => Name);
```

**Record**：

`Record` 是 TypeScript 提供的一个实用类型，用于创建键值对对象的类型。它允许你定义一个对象，其中所有的键都具有相同的类型，并且所有的值也具有相同的类型。

```ts
Record<Keys, Type>
    
interface FormItem {
  label: string;
  prop: string;
  span: number;
  components: Component[];
  rules?: { required: boolean; message: string; trigger: string }[];
}
type ItemsMap = Record<string, FormItem>;
```

**交叉和继承**：

```ts
import { AxiosResponse } from 'axios';

interface CustomResponse {
  code: number;
  data: any;
  ms: string;
}

type CombinedResponse = AxiosResponse & CustomResponse;
```

```ts
import { AxiosResponse } from 'axios';

interface CustomResponse {
  code: number;
  data: any;
  ms: string;
}

interface CombinedResponse extends AxiosResponse, CustomResponse {}
```

CombinedResponse接口继承了 `AxiosResponse` 和 `CustomResponse`，因此它同时包含了这两个接口中的所有属性



#### 在Vue3项目中的使用

```ts
<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  props: {
    message: String
  },
  setup(props) {
    console.log(props.message);
  }
});
</script>
```



#### 项目中ts相关问题





### Vue -18n

```js
import Vue from 'vue';
import VueI18n from 'vue-i18n';

Vue.use(VueI18n);

const i18n = new VueI18n({
  locale: 'zh-CN',
  messages: {
    'zh-CN': {
      welcome: '欢迎使用Vue.js',
    },
    'en-US': {
      welcome: 'Welcome to Vue.js',
    },
  },
});

new Vue({
  i18n,
  // ...
}).$mount('#app');
```

在上面的示例中，我们首先通过 `import` 将 Vue 和 VueI18n 引入，然后使用 `Vue.use()` 方法注册 VueI18n 插件。

接下来，我们创建了一个名为 `i18n` 的实例，并传入了一些配置参数。其中，`locale` 参数指定了当前的语言环境，`messages` 参数是一个对象，它按照语言代码为键，包含了对应的翻译内容。

最后，在创建 Vue 实例时，我们将 `i18n` 实例作为参数传入，从而将其注入到所有的子组件中，使其可以调用 𝑡函数进行翻译。*t*函数进行翻译。

在组件中使用t 函数非常简单，只需在模板中使用插值语法即可。下面是一个使用 $t 函数的示例：

```vue
<template>
  <div>
    <p>{{ $t('welcome') }}</p>
  </div>
</template>
```



### 问题记录

**1.todo**

todo



### 项目展示

todo
