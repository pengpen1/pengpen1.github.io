---
title: 爬虫学习笔记(一)
date: 2024-03-05 14:23:00
tags: python
categories: 其他
description: 笔者学习python爬虫的第一篇笔记。阅读时长：7min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/forest-6631518_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将初识爬虫。

### 什么是爬虫

爬虫是一个自动化程序，可以帮我们从一个个网页上获取我们想要的数据。

**爬虫工作原理：**

爬虫会模拟请求，用一些 Http 库向指定的服务器发送请求，再添加一些header信息假装自己是浏览器，拿到返回的数据后我们就要根据数据类型进行处理，有html、json、image等等类型，最后在将处理好的数据存储起来。





### 安装抓包工具

安装一下免费软件[Fiddler](https://www.telerik.com/download/fiddler)，Fiddler 是以代理web服务器的形式工作的，它使用代理地址:127.0.0.1，端口:8888。

Fiddler功能强大，可以拦截向服务器发送或服务器返回的请求，允许我们篡改数据，你对HTTP协议越了解，使用fiddler就会更得心应手，而越使用fiddler，你对HTTP协议的理解就越深，相辅相成。



### Fiddler功能介绍

**菜单栏(右上角)**

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240308150944.png)

File菜单

　　1、CaptureTraffic：可以控制是否把Fiddler注册为系统代理。

　　2、NewViewer：打开一个新的fiddler窗口

　　3、LoadArchive：用于重新加载之前捕获的以SAZ文件格式保存的数据包。

　　4、Save：支持以多种方式把数据包保存到文件中。

　　5、ImportSessions...：支持导入从其他工具捕获的数据包，也支持导入以其他格式存储的数据包。

　　6、ExportSessions...：把Fiddler捕捉到的回话以多种文件格式保存。

　　7、Exit：取消把Fiddler注册为系统代理，并关闭Fiddler。

Edit菜单

　　1、Copy：复制会话。

　　2、Remove：删除会话。

　　3、SelectAll：选择所有会话。

　　4、Undelete：撤销删除会话。

　　5、PasteasSession把剪贴板上的内容粘贴成一个或多个模拟的会话。

　　6、Mark：选择一种颜色标记选中会话。

　　7、UnlockforEditing解锁会话。

　　8、FindSession...打开FindSession窗口，搜索捕获到的数据包。

Rules菜单

　　1、HideImageRequest：隐藏图片回话。

　　2、HideCONNECTS：隐藏连接通道回话。

　　3、AutomaticBreakpoints：自动在[请求前]或[响应后]设置断点。IgnoreImage触发器控制这些断点是否作用于图片请求。

　　4、CustomizeRules...：打开Fiddler脚本编辑窗口。

　　5、RequireProxyAuthentication：，要求客户端安装证书。该规则可以用于测试HTTP客户端，确保所有未提交Proxy-Authorization请求头的请求会返回HTTP/407响应码。

　　6、ApplyGZIPEncoding：只要请求包含具有gzip标识的Accept-Encoding请求头，就会对所有响应使用GZIPHTTP进行压缩（图片请求除外）。

　　7、RemoveAllEncoding：删除所有请求和响应的HTTP内容编码和传输编码

　　8、Hide304s：隐藏响应为HTTP/304NotModified状态的所有回话。

　　9、RequestJapaneseContent：选项会把所有请求的Accept-Encoding请求头设置或替换为ja标识，表示客户端希望响应以日语形式发送。

　　10、User-Agents：把所有请求的User-Agent请求头设置或替换成指定值。

　　11、performance：模拟弱网测试速度。

Tools菜单

　　1、Options...：打开Fiddler选项窗口。

　　2、WinINETOptions...打开IE的Internet属性窗口

　　3、ClearWinINETCache：清空IE和其他应用中所使用的WinINET缓存中的所有文件。4、ClearWinINETCookies：清空IE和其他应用中所发送的WinINETCookie

　　5、TextWizard...：选项会启动TextWizard窗口，对文本进行编码和解码。

　　6、CompareSession：比较回话。

　　7、ResetScript：重置Fiddler脚本。

　　8、Sandbox：打开http://webdbg.com/sandbox/

　　9、ViewIECache:打开IE缓存窗口。



**工具栏(菜单栏下面)**

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240308150237.png)

1. windows解除AppContainer的限制的设置
2. 备注功能
3. 重新发送请求，快捷键：R键。
4. 删除请求
5. 当有请求杯断点时，点击去发送请求。
6. 流模式。(默认是缓冲模式)
7. 解码
8. 保持回话的数量。
9. 选择你想要抓包或者监听的程序
10. 查找
11. 保存所有会话，文件名以.saz为扩展名
12. 截图
13. 计时器
14. 快捷的打开IE浏览器
15. 清除IE缓存
16. 文本的编码解码工具
17. 分离面板
18. MSDN查询
19. 本机的信息



**状态栏(左下角)**

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240308145736.png)

1、显示的Fiddler是否处于捕捉状态(开启/关闭状态),可以点击该区域切换。

2、显示当前捕捉哪些进程。

　　AllProcesses捕获所有进程的请求

　　WebBrowsers?捕获Web浏览器的请求，应该特指IE

　　Non-Browser?捕获非Web浏览器的请求

　　HideAll???隐藏所有请求

3、显示当前断点设置状态，通过鼠标点击切换。有三种：

　　不设置断点

　　所有请求在断点处被暂停

　　所有响应在断点处被暂停

4，显示当前共捕获了多少回话(如：300，表示共捕获了300个会话，如：10/300，表示当前选择10个会话，共捕获300个会话)。

5，第五区块，描述当前状态。

　　如果是刚打开Fiddler，会显示什么时间加载了CustomRules.js；如果选择了一个会话，会显示该会话的URL；如果在命令行输入一个命令，就会显示命令相关信息。



**标签页(右侧，菜单栏下面)**

Statistics统计页签，可以查看传输的字节数，消耗时间等等。

inspectors检查页签，可以查看具体的请求和响应报文，上为请求，下为响应。

AutoResponse自动响应页签，可以定义规则，监听指定请求，返回指定数据，比如你可以监听[百度网址](https://www.baidu.com/)，然后返回你本地的一个图片。

composer构建页签，支持手动构建和发送请求，还可以从web session列表中拖曳session， 把它放到composer选项卡中以克隆该请求， 点击Execute按钮， 把请求发送到服务端。

Filters过滤页签，使用过滤Filters对左侧的数据流列表进行过滤， 可以标记、 修改或隐藏某些特征的数据流。

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240307162635.png)



Hide if url contains用的挺多的，除了输入字符串，还可以输入正则，比如`REGEX:(?insx)/[^\?/]*\.(css|ico|jpg|png|gif|bmp|wav)(\?.*)?$`，REGEX: 表示启用正则表达式(?insx) 设置正则解释的规则，忽略大小写等。此表达式表示过滤掉 url 中包括 css、ico、jpg 等后缀的请求。

Timeline时间轴页签，可以选择左侧会话中的一个或多个(按CTRL)，便会以图表形式显示指定内容从服务端传输到客户端的时间。



**快捷键**

按住**ALT+F11**拦截所有的返回，按住**F11**拦截所有请求，按住**Ctrl+x** 清屏。



**命令行**

Fiddler的左下角有一个命令行工具叫做QuickExec,允许你直接输入命令。常见命令：

**help** ： 打开官方的使用页面介绍， 所有的命令都会列出来

**cls** ： 清屏 (Ctrl+x 也可以清屏)

**select** ： 选择会话的命令， 选择所有相应类型select image、select css、select html

**?sometext** ： 查找字符串并高亮显示查找到的会话列表的条目，？[http://qq.com](https://link.zhihu.com/?target=http%3A//qq.com)

**>size** : 选择请求响应大小小于size字节的会话

**=status/=method/@host**:查找状态、方法、主机相对应的session会话，=504，=get，@[http://www.qq.com](https://link.zhihu.com/?target=http%3A//www.qq.com)

**quit**：退出fiddler

Bpafter，Bps, bpv, bpm, bpu这几个命令主要用于批量设置断点

Bpafter xxx: 中断 URL 包含指定字符的全部 session 响应

Bps xxx:中断 HTTP 响应状态为指定字符的全部 session 响应。

Bpv xxx:中断指定请求方式的全部 session 响应

Bpm xxx:中断指定请求方式的全部 session 响应，等同于bpv xxx

Bpu xxx:与bpafter类似。



### HTTP简单介绍

HTTP协议，支持客户/服务器模式。简单快速，无连接（也可以持久性连接，在单个TCP连接上发送多个HTTP请求），无状态。

HTTP协议：默认端口：80

HTTPS协议：HTTP协议+SSL安全传输协议：默认端口443



###  HTTP协议请求详解

请求行：包括请求方法、请求的URL以及HTTP协议版本，例如：GET /index.html HTTP/1.1。

请求头：

- Accept 指定客户端能够接收的内容类型

- Accept-Charset 浏览器可以接受的字符编码集。

- Accept-Encoding 指定浏览器可以支持的web服务器返回内容压缩编码类型。

- Accept-Language 浏览器可接受的语言

- Cookie HTTP请求发送时，会把保存在该请求域名下的所有cookie值一起发送给web服务器。

- Content-Length 请求的内容长度

- Content-Type 请求的与实体对应的MIME信息

- Date 请求发送的日期和时间

- Host 指定请求的服务器的域名和端口号

- Referer 先前网页的地址，当前请求网页紧随其后,即来路

- User-Agent 发出请求的用户信息
- Cache-Contro  用于控制缓存行为
- Connection 用于指定连接的管理方式，如是否保持连接
- Pragma  向服务器传递指令，通常用于缓存控制

空一行

请求正文：对于一些HTTP请求，可能会包含请求正文，例如在POST请求中发送的表单数据就包含在请求正文中



### 疑问解答

1.每次进入为什么有这个弹窗？

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/1709693791141.png)

上面这个说的是，windows用了一个AppContainer的技术，可能干扰部分应用和Edg浏览器的流量捕获，你可以使用左上角的WinConfig解除这个限制，问你需要了解更多不，选cancel的话就不会在出现这个弹框。