---
title: Hexo框架、GitHub Pages与自动化部署
date: 2023-08-29 16:20:44
tags: 自动化部署
categories: 其他
description: 在这篇记录中，我们将探讨如何利用GitHub Actions实现博客的自动化部署。阅读时长：3min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/background-8033597_1280.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将探讨[Hexo框架](https://github.com/hexojs/hexo)、GitHub Pages以及如何使用GitHub Actions实现自动化部署的原理与流程。

## Hexo框架

Hexo是一个快速、简单且强大的静态博客框架，它允许您使用Markdown等标记语言编写博客内容，然后将其自动转换为静态HTML页面。

## GitHub Pages

GitHub Pages是GitHub提供的免费静态网站托管服务。它允许GitHub用户将静态网站部署到GitHub的服务器上，并通过特定的GitHub Pages网址进行访问。GitHub Pages支持自定义域名、HTTPS、Jekyll等功能，使其成为托管个人博客和项目文档的理想选择。

## GitHub Actions

GitHub Actions是GitHub提供的自动化CI/CD工具，它可以响应GitHub仓库中的事件并执行自定义的工作流程。我们可以使用GitHub Actions来实现自动化部署。

## 自动化部署流程与原理

自动化部署是一种将Hexo博客自动构建和部署到GitHub Pages的方式，这可以大大简化发布流程并确保博客内容的实时更新。

### 部署流程

1. **触发条件**：配置GitHub Actions工作流程，以响应特定事件，例如代码推送到`master`分支。

2. **工作流程配置**：在工作流程的YAML文件中定义构建和部署步骤。这包括设置Node.js环境、安装Hexo依赖、构建博客、部署到GitHub Pages等。

3. **自动执行**：当触发条件满足时，GitHub Actions会自动执行工作流程。

4. **构建**：工作流程会自动执行Hexo项目的构建命令，生成静态HTML文件。

5. **部署**：使用GitHub Actions中的Action，将构建后的静态文件发布到GitHub Pages。这通常需要提供GitHub Token以获得必要的权限。

6. **结果**：GitHub Actions会将执行结果报告给您，包括部署是否成功以及任何错误信息。

通过这种方式，您可以自动化Hexo博客的构建和部署，使博客内容始终保持最新状态，无需手动干预。

### 部署示例
在存库中建立 .github/workflows/pages.yml 文件，内容如下
```yml
name: Pages # 部署文件名称

on: # 触发条件，在这个例子中，工作流程会在代码推送（`push`事件）到`master`分支时触发
  push:
    branches:
      - master # default branch

jobs:
  pages:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v2 # 这个步骤使用另一个官方Action来设置Node.js环境
      - name: Use Node.js v16.13.0.x
        uses: actions/setup-node@v2 # GitHub Actions提供的官方Action，将代码仓库检出到工作环境中
        with:
          node-version: "v16.13.0"
      - name: Cache NPM dependencies
        uses: actions/cache@v2 # 这个步骤用于缓存npm依赖项，以提高构建速度
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install # 运行步骤，用于安装Hexo项目的npm依赖项
      - name: Build
        run: npm run build # 这个步骤运行Hexo项目的构建命令
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3 # 第三方action，用于将Hexo博客的生成文件发布到GitHub Pages。它需要一个GitHub Token
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

## 结论

Hexo框架、GitHub Pages和自动化部署工具（如GitHub Actions）的结合为博客作者提供了一个强大而方便的方式来创建、管理和发布博客内容。这些工具使博客的维护变得更加高效，让作者可以更专注于创作内容而不是繁琐的部署过程。
