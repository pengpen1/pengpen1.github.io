---
title: PicGo增加插件
date: 2025-01-17 10:20:11
tags: 图床
categories: 其他
description: 在这篇记录中，我们将为PicGo增加插件。阅读时长：3min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20250116154107267.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将为PicGo增加插件，如果不只是picGo是做什么的可以去查看往期文章，[PicGo+gitHub+jsdelivr实现加速图床](https://pengpen1.github.io/2023/09/01/%E5%85%B6%E4%BB%96-picGo+gitHub+jsdelivr%E5%AE%9E%E7%8E%B0%E5%8A%A0%E9%80%9F%E5%9B%BE%E5%BA%8A/)。



### 步骤

`update：2025/1/16`最新PicGo推出了插件功能，我们也来试试吧

首先找到PicGo配置文件所在目录，windows里你可以在：

`C:\Users\你的用户名\AppData\Roaming\picgo\data.json`找到它。

在linux里你可以在：

`~/.config/picgo/data.json`里找到它。



接下来命令行cd进入该项目（目录），或者在路径那直接输入`cmd`然后回车

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/pico命令行.png)

安装插件

```shell
npm install <your-plugin-name>
例如：
npm install picgo-plugin-github-plus
npm install picgo-plugin-compress
```

然后在picGo项目就会多出一个node_modules文件夹，我们重启一下picGo，就能看到刚刚安装的插件啦

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20250116152658116.png)



补充下[github-plus](https://github.com/zWingz/picgo-plugin-github-plus?tab=readme-ov-file)插件如何使用吧

首先我们需要配置下githubPlus，和配置github图床类似，有个需要注意的是customUrl需要更完整些（最终查找路径`${customUrl}/path/filename.jpg`）：

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20250116153600555.png)

然后将默认图床更换成githubPlus，需要拉去origin（图床）所有图片的话，只需在插件设置那点击`pull origin`  

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20250116154107267.png)



### 疑问

**1.上传失败，提示检查配置项以及网络，随后提示上传失败{}，改怎么解决？**

有小伙伴问我这个是怎么回事，这里统一说一下，遇到这种情况，一般都是Token过期导致的。要确定的话，只需在设置那，看一下日志：

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240912162303.png)

401就代表无权访问，也就是Token过期了，重新去Settings > Developer settings >Personal access tokens>Tokens中创建一个访问token即可。



