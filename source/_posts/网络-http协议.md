---
title: http协议
date: 2024-01-21 13:15:00
tags: 网络
categories: 网络
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/street-8434099_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**本文主要是带领大家学习网络是如何传输的，以及http协议。

## 网络的分层架构

我们一开始接触网络的时候，看到什么应用层，数据链路层就感到头疼，就会想为什么要这么多层呢？这是为了有更好的扩展性，可维护性，从物理层到应用层，每一层都有其特定的职责。这种分层设计在网络通信中被广泛应用，以实现高效、可靠的数据传输。

数据在传输的过程中，可能出现丢包，数据重复的问题，所以网络要效验数据的完整性，不可谓不复杂，所以让每一层专注于自己的职责，只管对接上一层和一层的接口就行，这样后面更换这一层的程序或者进行扩展就会更方便。这里我们来看下TCP/IP协议簇：

| 名称       |                                |
| ---------- | ------------------------------ |
| 应用层     | DNS，HTTP，SSH，SMTP，FTP...   |
| 传输层     | TCP，UDP，SCTP(流控制传输协议) |
| 网络层     | IPv4，IPv6，ARP，ICMP          |
| 数据链路层 | 以太网(Ethernet)，无线LAN      |
| 物理层     | 光纤，双绞线电缆，无线设备     |





### 参考链接

- [1.从一个HTTP请求来看网络分层原理_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1V54y1y7c4?p=1)

