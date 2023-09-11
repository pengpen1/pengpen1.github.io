---
title: PicGo+gitHub+jsdelivr实现加速图床
date: 2023-09-01 10:20:11
tags: 图床
categories: 其他
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20230908174847.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将探讨如何利用PicGo+gitHub+jsdelivr实现加速图床。

## PicGo

[PicGo](https://molunerfinn.com/PicGo/)是一个开源的图片上传与管理工具。

## jsdelivr

jsdelivr是免费、快速且可靠的开源CDN（Content Delivery Network，即内容分发网络。其目的是通过在现有的Internet中增加一层新的网络架构，将网站的内容发布到最接近用户的网络“边缘”，使用户可以就近取得所需的内容，提高用户访问网站的响应速度）。使用也极其简单，直接修改链接皆可。

## 步骤

### 创建仓库

在gitHub上创建一个公共仓库，用于存放图片。

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20230907202830.png)

### 获取访问token

在Settings > Developer settings >Personal access tokens>Tokens中创建一个访问token。用途随意，过期时间看着办，第一个控制仓库的权限给它选上，其他默认皆可。注意这里创建完毕后要立马复制token。

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20230907203052.png)

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20230907203346.png)

### 下载PicGo

[picGo](https://molunerfinn.com/PicGo/)，widows选-x64.exe结尾的，ios选-64.dmg结尾的。

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20230907204038.png)

### 设置GitHUb图床

打开PicGo，在图床设置中选择GitHub，更具提示填写相应配置。在设定自定义域名这，我们需要把jsdelivr使用上：

```js
https://cdn.jsdelivr.net/gh/用户名/仓库名

例如：https://cdn.jsdelivr.net/gh/pengpen1/blog-images
```

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20230907204416.png)

## 结论

这样你就可以获取一个免费的加速图床啦！快去上传区上传第一张图片吧！

