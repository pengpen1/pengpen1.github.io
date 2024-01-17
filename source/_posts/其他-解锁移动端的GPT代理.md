---
title: 解锁移动端的GPT代理
date: 2024-01-16 5:40:00
tags: VPN
categories: 其他
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20231207132922.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将探讨一下为什么有些自建节点在GTPWEB端可行，但是在安卓端登录就报错这个问题，请勿用于非法用途！

### **问题描述**

使用x-ui和reality实现的自建节点在web端使用GTP没问题，但是在移动端使用就出现下面的报错。下面我们先从基础知识开始，一步步带大家解决这个问题。

<img src="https://cdn.jsdelivr.net/gh/pengpen1/blog-images/e62afa6b65a9c4aede62b023b5d221b.jpg" alt="图片" style="width:50%">



### 基础知识

**1.判定节点情况**

有些节点是本身该地区就不被GTP支持，有些是没有解锁GTP移动端的服务（**默认未配置或未开放对移动端（手机、平板等）访问OpenAI GPT服务的权限**），比如我所使用的racknerd节点。那么如何判断自己属于哪种情况呢？

方法1，访问这个网址：https://ios.chat.openai.com/public-api/mobile/server_status/v1

方法2，curl安卓端服务

```shell
curl andriod.chat.openai.com
```

如果出现下方的内容就是和我一样，表示节点未解锁GTP移动端服务：

```
"cf_details": "Something went wrong. You may be connected to a disallowed ISP. If you are using VPN, try disabling it. Otherwise try a different Wi-Fi network or data connection. (1)
```

如果是下方的内容，表示已解锁GTP移动端服务：

```
Request is not allowed. Please try again later
或者
status：normal
```



**2.节点类型**

通常常见的IP属性分为三种:ISP、hosting、business

1. **ISP (Internet Service Provider):** 这种节点是由互联网服务提供商（ISP）提供的。ISP节点通常用于提供一般的互联网连接服务，例如家庭宽带、企业网络等。这些节点的IP地址通常归属于互联网服务提供商。
2. **Hosting:** Hosting节点是由托管服务提供商提供的，用于托管网站、应用程序或其他在线服务。这些节点通常专注于提供服务器托管服务，为用户提供计算资源和存储空间。
3. **Business:** Business节点是由企业或组织自己搭建和维护的。这种类型的节点通常用于企业内部的网络服务，如内部网站、应用程序服务器等。它们可能不对外提供服务，而是为企业内部员工或特定用户群体提供服务。

这些节点类型有助于区分服务器的使用场景和提供者。例如，ISP节点通常是公共互联网服务的一部分，而Hosting节点则专注于为客户提供托管服务。 Business节点则是由企业用于满足内部需求的服务器。

对于科学上网来说，ISP类型的更纯净些，所以有些文章就是让你换节点来解决移动端被封锁的问题。



### DNS解锁

**1.什么是DNS**

**DNS（Domain Name System）** 是互联网上用于将域名转换为与之关联的IP地址的系统。它充当了互联网的地址簿，帮助用户通过易记的域名访问网站，而不是记住复杂的IP地址。

**2.DNS解锁**

DNS解锁是通过更改DNS服务器设置，绕过地理限制或访问受限内容的一种方法。手动选择DNS提供商，Google DNS、OpenDNS、Cloudflare DNS。更改配置为使用所选的DNS提供商的服务器地址以此实现DNS解锁。



### warp解锁

**1.什么是warp代理**

Warp代理是由Cloudflare提供的一种虚拟私人网络（VPN）服务，基于**WireGuard协议**，可以提供更安全和更快速的互联网连接。它通过将用户的网络流量路由通过Cloudflare的全球网络来加密和保护数据，同时还能提供更快的访问速度和更稳定的连接。有免费版和 WARP+付费版本，这里要注意一下，如果你vps所在地区本身就不被openai支持，那通过warp代理出来的ip也是不被支持的。

**WireProxy** 是一个WireGuard客户端，它以Socks5代理的形式暴露自身，允许用户在WireGuard网络上建立连接。

warp代理和wrap术语（指通过一种代理技术将网络流量进行包装或伪装，以隐藏真实的网络访问目的地）要区分哦。

**2.什么是socks5代理**

SOCKS5（Socket Secure 5）是一种网络协议，用于在网络中建立代理连接。它允许用户通过代理服务器进行网络连接，提供一种通用的、灵活的代理服务。

**3.vps实现wrap代理**

注意，我这里用的是x-ui+reality，如果是其他协议请在网上查询对于文章。核心原理就是用warp延伸出的socks5端口访问gtp移动端服务。

**第一步**

执行wrap脚本：

```shell
wget -N https://gitlab.com/fscarmen/warp/-/raw/main/menu.sh && bash menu.sh
```

然后选择语言2，接下来他会扫描你vps的配置自己安装依赖，然后出现菜单

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240117145134.png)

然后选择13，创建socks5代理，这里用默认就行了，不然需要在下面的socks配置中改写成你选的端口。再然后选择免费版的。



**第二步**

在x-ui面板上修改xray配置，其实就是多了一个socks出站和一个路由规则，凡是openai的请求全部交给warp的socks端口处理。

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240117150109.png)

```json
{
    "api": {
        "services": [
            "HandlerService",
            "LoggerService",
            "StatsService"
        ],
        "tag": "api"
    },
    "inbounds": [
        {
            "listen": "127.0.0.1",
            "port": 62789,
            "protocol": "dokodemo-door",
            "settings": {
                "address": "127.0.0.1"
            },
            "tag": "api"
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom",
            "settings": {}
        },
        {
            "protocol": "blackhole",
            "settings": {},
            "tag": "blocked"
        },
        {
            "tag": "warp",
            "protocol": "socks",
            "settings": {
                "servers": [
                    {
                        "address": "127.0.0.1",
                        "port": 40000
                    }
                ]
            }
        }
    ],
    "policy": {
        "levels": {
            "0": {
                "handshake": 10,
                "connIdle": 100,
                "uplinkOnly": 2,
                "downlinkOnly": 3,
                "statsUserUplink": true,
                "statsUserDownlink": true,
                "bufferSize": 10240
            }
        },
        "system": {
            "statsInboundDownlink": true,
            "statsInboundUplink": true
        }
    },
    "routing": {
        "rules": [
            {
                "type":"field",
                "domain":[
                    "geosite:openai"
                ],
                "outboundTag":"warp"
            },
            {
                "inboundTag": [
                    "api"
                ],
                "outboundTag": "api",
                "type": "field"
            },
            {
                "ip": [
                    "geoip:private"
                ],
                "outboundTag": "blocked",
                "type": "field"
            },
            {
                "outboundTag": "blocked",
                "protocol": [
                    "bittorrent"
                ],
                "type": "field"
            }
        ]
    },
    "stats": {}
}
```

最后重启x-ui即可。



### **参考链接**

- [顾佳凯的网络日志](https://blog.gujiakai.top/2023/10/chatgpt-android-error-disallowed-isp-solution)
- [warp](https://inurl.top/archives/datizi/)