---
title: mall-front
date: 2024-06-28 15:34:00
categories: 项目
description: 记录一下写mall-front的历程。阅读时长：2min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/book-2020460_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**私人商城项目的前台页面，三端兼容，基于vue3、element-plus、typeScript、tailwindcss实现。



### 创建项目

项目最好安装扩展`Tailwind CSS IntelliSense`、`Prettier - Code formatter`

#### 集成tailwindcss

安装 Tailwind CSS 及其依赖。

```shell
npm install -D tailwindcss postcss autoprefixer
```

运行以下命令生成 `tailwind.config.js` 和 `postcss.config.js` 文件：

```shell
npx tailwindcss init -p
```

这将创建两个配置文件：

- `tailwind.config.js`
- `postcss.config.js`

编辑 `tailwind.config.js` 文件，将 `content` 部分修改为包含你的 Vue 文件：

```js
/** @type {import('tailwindcss').Config} */
export default {
    content: ["./index.html", "./src/**/*.{vue,js,ts,jsx,tsx}"],
    theme: {
        extend: {},
    },
    plugins: [],
}
```

创建或编辑 `src/assets/tailwind.css` 文件，添加以下内容：

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

编辑 `src/main.ts` 文件，添加对 Tailwind CSS 的引入：

```js
import { createApp } from 'vue'
import App from './App.vue'
import './assets/tailwind.css'

createApp(App).mount('#app')
```

安装`prettier-plugin-tailwindcss`，可以帮助我们对class进行排序，规则提取自Tailwind 。

```shell
npm install -D prettier prettier-plugin-tailwindcss
```

然后我们来测试小集成成功没，随便加些class

```css
<p class="text-red-500 underline">test tailwindcss</p>
```

安装了上面说的扩展后，鼠标悬浮还能看到具体的css的代码

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240628161530.png)

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240628161737.png)

#### 项目结构

安装 ESLint 和 Prettier 以确保代码风格一致，并检测潜在的错误：

```shell
npm install -D eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-plugin-vue
npm install -D prettier eslint-config-prettier eslint-plugin-prettier
```

创建 `.eslintrc.js` 文件：

```js
module.exports = {
  root: true,
  env: {
    node: true,
  },
  extends: [
    'plugin:vue/vue3-essential',
    'eslint:recommended',
    '@vue/typescript/recommended',
    'prettier',
    'plugin:prettier/recommended',
  ],
  parserOptions: {
    ecmaVersion: 2020,
  },
  rules: {
    'vue/multi-word-component-names': 'off',
    '@typescript-eslint/no-unused-vars': 'off',
    '@typescript-eslint/explicit-module-boundary-types': 'off',
    'prettier/prettier': ['error', { endOfLine: 'auto' }],
  },
};
```

`  root: true,`：表示这是项目的根配置文件，ESLint 不会再向上查找其它的配置文件。

`node: true,`：定义了代码运行的环境。这里设置 `node: true`，表示代码将运行在 Node.js 环境中，Node.js 全局变量和作用域将会被预定义。

`'plugin:vue/vue3-essential'`：使用 Vue 3 的基本规则。

`'eslint:recommended'`：使用 ESLint 推荐的基本规则。

`'@vue/typescript/recommended'`：使用 Vue 官方推荐的 TypeScript 规则。

`'prettier'`：禁用所有不必要或可能与 Prettier 冲突的 ESLint 规则。

`'plugin:prettier/recommended'`：启用 `eslint-plugin-prettier` 并将 Prettier 作为 ESLint 规则来运行，显示 Prettier 错误为 ESLint 错误。这一项需要放在最后。

  `ecmaVersion: 2020`：指定 ECMAScript 的版本，这里是 ECMAScript 2020。

`rules`：自定义的 ESLint 规则。

`'vue/multi-word-component-names': 'off'`：关闭 Vue 组件名称必须是多词的规则。

`'@typescript-eslint/no-unused-vars': 'off'`：关闭 TypeScript 未使用变量的规则。

`'@typescript-eslint/explicit-module-boundary-types': 'off'`：关闭要求显式声明函数和类方法的返回类型的规则。

`'prettier/prettier': ['error', { endOfLine: 'auto' }]`：将 Prettier 的格式化规则作为 ESLint 错误处理，并设置 `endOfLine` 选项为 `auto`，这将自动处理行结束符（例如 LF 或 CRLF）。



创建 `.prettierrc` 文件：

```json
{
  "singleQuote": true,
  "semi": false,
  "trailingComma": "es5"
}
```



```shell
npm i @element-plus/icons @vueuse/core axios element-plus vue-i18n vue-router vuex
```

然后就是创建文件夹，对项目进行架构。文件结构：

```lua
my-vue-app/
├── public/
│   └── favicon.ico
├── src/
│   ├── assets/
│   │   └── images/
│   ├── components/
│   │   └── MyComponent.vue
│   ├── composables/
│   │   └── useMyComposable.ts
│   ├── layouts/
│   │   └── DefaultLayout.vue
│   ├── pages/
│   │   └── Home.vue
│   ├── router/
│   │   └── index.ts
│   ├── store/
│   │   └── index.ts
│   ├── styles/
│   │   ├── tailwind.css
│   │   └── globals.css
│   ├── utils/
│   │   └── helpers.ts
│   ├── App.vue
│   ├── main.ts
│   └── vite-env.d.ts
├── .editorconfig
├── .eslintrc.js
├── .gitignore
├── index.html
├── package.json
├── postcss.config.js
├── tailwind.config.js
├── tsconfig.json
├── vite.config.ts
└── yarn.lock / package-lock.json
```



### 问题记录

**1.todo**

todo



### 项目展示

todo
