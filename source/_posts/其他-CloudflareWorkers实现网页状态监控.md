---
title: Cloudflare Workers实现网页状态监控
date: 2024-05-08 14:25:44
categories: 其他
description: 在这篇记录中，我们将探讨如何利用Cloudflare Workers实现网页状态监控。阅读时长：1min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/books-5937716_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将探讨如何利用Cloudflare Workers实现网页状态监控。



### 第一步

fork下面这个仓库

`https://github.com/eidam/cf-workers-status-page`



### 第二步

开始部署，点击Depoly with Workers链接登录并授权Cloudflare访问Github仓库。

```
https://deploy.workers.cloudflare.com/?url=https://github.com/pengpen1/cf-workers-status-page
```

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240508145951.png)

填API令牌

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240508155009.png)

填账户ID

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240508155106.png)

记得在 Github > actions > Deploy 中开启部署 (fork项目是默认是关闭的)



### 第三步

选择 config.yaml，更改想要运行监控的网站链接

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240508155601.png)



### 第四步

选择wrangler.toml Corn，修改定时更新 ，更改为crons = ["*/2* *"]

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240508155620.png)



### 参考链接

- [利用Cloudflare Workers实现网页状态监控)](https://galkm.com/default/cloudflare-workers.html)

