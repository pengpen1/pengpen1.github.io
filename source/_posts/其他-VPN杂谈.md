---
title: VPN杂谈
date: 2024-01-02 9:40:00
tags: VPN
categories: 其他
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20231207132915.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将探讨一下V2ray以及reality，最后使用x-ui以及realtiy搭建一个VPN。注：本文章仅供学习使用，请勿用于非法用途！

### 基础知识

**1.vps的挑选**

https://my.racknerd.com/aff.php?aff=5059&pid=792

[Mass VPS hosting on Enterprise equipment - BandwagonHost VPS (bwh81.net)](https://bwh81.net/)

选适合自己的就行

**2.ssl证书**

SSL证书是数字证书的一种，用于实现HTTPS协议，通过SSL证书，可以实现网站数据加密传输，防止数据被截获或窃取，提高网站的安全性。

在获取SSL证书的过程中，**需要开放80和443端口**。80端口是HTTP协议的默认端口，而443端口是HTTPS协议的默认端口。如果VPS运营商开启了防火墙，并且没有放行这两个端口，那么就无法正常获取SSL证书，从而导致网站无法使用HTTPS协议进行访问。

因此，在获取SSL证书之前，需要先登录VPS管理后台，放行80和443端口。如果已经拥有SSL证书，也可以上传自定义证书，以跳过申请证书这一步。

同时，需要注意的是，不同的VPS运营商可能默认开启或关闭防火墙设置，因此在选择VPS运营商时需要仔细了解其服务配置，以免出现不必要的麻烦。

**3.UDP协议和隧道**

UDP（用户数据报协议）是一种无连接的协议，它与TCP（传输控制协议）不同，不需要进行三次握手建立连接。UDP报文头部开销小，只有8个字节，相对TCP的20字节来说，更加简单。

UDP协议的特点如下：

1. 无连接：与TCP不同，UDP是无连接的协议，数据传输前不需要经过三次握手建立连接。
2. 面向报文：每一条UDP报文就是一份完整的报文，同时源端口和目的端口在传送数据时是需要知道的。
3. 不可靠性：UDP尽最大努力交付，但不保证可靠交付。
4. 面向无连接：UDP不需事先建立连接，因此减少了开销和发送数据的时延。
5. 头部开销小：只有8个字节的头部开销。
6. 提供面向事务的简单的不可靠信息传送服务。
7. 适用范围有限：如QQ聊天信息等。

隧道则是一种网络通信技术，通过这种技术可以将一个网络协议的数据包封装在另一个网络协议的数据包中，以便在网络中传输。隧道可以用来解决网络安全、数据传输等问题。例如，可以通过将数据包封装在IPsec协议中来保护数据的机密性和完整性。



### V2Ray和xray

**1.V2Ray配置**

这两个都是平台，可以挂载各种协议

[前言 · V2Ray 配置指南|V2Ray 白话文教程 (selierlin.github.io)](https://selierlin.github.io/v2ray/)

[V2ray的VLESS协议介绍和使用教程 - V2ray XTLS黑科技 (v2xtls.org)](https://v2xtls.org/v2ray的vless协议介绍和使用教程/)

VMESS，即最普通的V2ray服务器，没有伪装，也不是VLESS
VMESS+KCP，传输协议使用mKCP，VPS线路不好时可能有奇效
VMESS+TCP+TLS，带伪装的V2ray，不能过CDN中转
VMESS+WS+TLS，即最通用的V2ray伪装方式，能过CDN中转，推荐使用
VLESS+KCP，传输协议使用mKCP
VLESS+TCP+TLS，通用的VLESS版本，不能过CDN中转，但比VMESS+TCP+TLS方式性能更好
VLESS+WS+TLS，基于websocket的V2ray伪装VLESS版本，能过CDN中转，有过CDN情况下推荐使用
VLESS+TCP+XTLS，目前最强悍的VLESS+XTLS组合，强力推荐使用（但是支持的客户端少一些）
trojan，轻量级的伪装协议
trojan+XTLS，trojan加强版，使用XTLS技术来提升性能

**注意：**目前一些客户端不支持VLESS协议，或者不支持XTLS，请按照自己的情况选择组合

**2.V2Ray一键脚本**

这个脚本主要用于设置和配置V2Ray，包括检查系统环境、获取公网IP地址、检测BitTorrent软件等，如何配置v2ray可自定义，支持常规VMESS协议、VMESS+websocket+TLS+Nginx、VLESS+TCP+XTLS、VLESS+TCP+TLS等多种组合，支持CentOS 7/8、Ubuntu 16.04以上、Debian 8以上系统，以及相关衍生系统。注意如果用VMESS+WS+TLS或者VLESS系列协议，则还需一个域名

```shell
bash <(curl -sL https://storage.googleapis.com/tiziblog/setup.sh)
```



**3.Reality协议**

我们都知道， 普通的 `TLS` 代理的一个重要弱点就是各种加密进行套娃，虽然加密包的外观让防火墙无法进行分辨，但是加密套娃无可避免的一个点就在于，它会在每个包都增加一个数据包头，那加密的层数越多，包的头也会越多，这个增量虽然不大，但这个数据可能具有某些统计学的一些特征。若是发现这个特征嘞，是的，那 `TLS` 的代理也就不太安全了。

想当初，`RPRX` 发布了 `XTLS`，而 `XTLS` 其主要原因就是为了减少额外的加密，在 `TLS` 代理的特征被暴露以后，`Xray-core 1.8.0` 马上推出了新的流控 —— `Vision`，至此，当使用 `Vision` 传输 `TLS1.3` 的数据时，`99%` 的数据包，几乎拥有完美的流量特征，因为他是原始数据，没有经过任何的加工。

然而在此之外，今年的三月，`Xray-core 1.8.0` 又进一步推出了 REALITY 协议来取代传统的 TLS 服务，这样可以消除服务端 TLS 的指纹特征，仍然具有前向保密性，而且证书链也是攻击无效，那这样安全性的确就超越了常规的 TLS，关键可以指向别人的网站，所以无须自己购置域名，布置证书等，而且，不必使用 VPS 的 `443` 端口，所以，的确是方便和安全了不少。甚至，在我自己长达半年的使用过程中 `Reality + Vision`，没有出现任何问题，延迟、速率也是很不错。



### x-ui和reality

**1.安装BBR拥塞控制算法（非必须）**

BBR的主要思想是通过测量网络的带宽和往返时间（RTT）来动态调整发送数据的速率，以最大化网络利用率并减小排队延迟。BBR试图估计网络的瓶颈带宽，并维持数据在网络中的流动，以避免拥塞。

BBRplus 是对 Google BBR（Bottleneck Bandwidth and Round-trip propagation time）的一个修改版本，它旨在进一步改进拥塞控制算法，以提供更好的网络性能。

```shell
//使用不卸载内核版
wget -N --no-check-certificate "https://github.000060000.xyz/tcpx.sh" && chmod +x tcpx.sh && ./tcpx.sh

//下面这个是我当前用的脚本
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```

脚本自动安装的锐速内核，我们选择2，安装BBRplus内核，然后会提示我们重启VPS，我们输入y进行重启。然后在一次运行上面的脚本，状态就变成了已安装BBRplus内核但是未启动，我们只需输入7即可完成BBRplus加速。

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240111173321.png)

要验证成功没有也简单，输入下面命令返回bbrplus即表示成功了

```shell
sysctl net.ipv4.tcp_congestion_control
```



**2.安装x-ui**

```shell
bash <(curl -Ls https://raw.githubusercontent.com/FranzKafkaYu/x-ui/master/install.sh)
```

然后是取名，设置端口，我这里是4545

然后输入x-ui启动

```shell
x-ui
ctrl+c 退出查看日志状态
firewall-cmd --zone=public --add-port=1935/tcp --permanent
netstat -ntlp   //查看当前所有tcp端口·
```

这里可能遇到在页面上访问不到x-ui的情况，这是因为你的服务器没有开发对应的端口，这里教大家解决一下：

```shell
systemctl start firewalld //开启防火墙
firewall-cmd --zone=public --add-port=441/tcp --permanent //开发指定端口，这里是1935
firewall-cmd --reload  // 重启防火墙
netstat -ntlp   //查看当前所有tcp端口
```

**3.添加reality**

这里需要xray为1.8.0以上

开启reality，添加用户中，flow选择xtls-rprx-vision，最后点添加即可

   ![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/1704952057994.png)

注意，需要检查你的vps服务器是否开了对应端口，没开的话，按照上面的步骤打开一下端口。

最后把这个链接复制到小火箭或者v2RayN就可以啦。



### **参考链接**

- [手把手教你如何自己搭梯子（科学上网、轻松访问油管） - 免费搭建梯子 - 实验室设备网 (zztongyun.com)](https://www.zztongyun.com/article/免费搭建梯子)
- [【教程】如何搭建梯子（VPN）？-顶级索引 (inurl.top)](https://inurl.top/archives/datizi/)
- [搬瓦工搭建V2ray科学上网新手指南 – Clash 官网导航 (clashxhub.com)](https://clashxhub.com/banwagon-v2ray/)
- https://blog.mareep.net/posts/40290/https://artitalk.js.org/)