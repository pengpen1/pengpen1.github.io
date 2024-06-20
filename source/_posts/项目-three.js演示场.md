---
title: three.js演示场
date: 2024-06-18 13:44:00
categories: 项目
description: 记录一下从零起一个新项目。阅读时长：2min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240620093806.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**因为要学习three.js，又不能总拿公司的项目当实验场所，索性就新起个项目当作演示场，这里顺便记录下步骤。



### 创建项目

本次技术选型准备采用最新版本的vue3、vite、element-plus

```shell
npm init vite@latest
```

执行该命令后，系统会询问一些问题，如项目名称、使用的框架（选择 Vue）、是否使用 TypeScript 等

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240618140009.png)

然后试试项目能否跑起来，没问题的话，删除脚手架默认提供的页面组件，并按照自己的架构设计创建文件夹，最后把README.md文件加上：

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240618140249.png)

然后我们在GitHub或者其他远程托管仓库中简历新仓库

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240618135440.png)

然后用git命令初始化一下我们的项目、添加暂存区、提交、重命名主分支(有些版本默认是master)、设置远程仓库地址、推送到远程仓库

```shell
git init
git add .
git commit -m "[init]初始化项目结构"
git branch -M main
git remote add origin https://github.com/pengpen1/three-demo-site.git
git push -u origin main
```

然后在GitHub刷新一下就能看到我们新建的项目啦

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240619102818.png)



### 项目展示

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240620093606.png)

[项目地址](https://github.com/pengpen1/three-demo-site)


