---
title: 破解office
date: 2024-07-13 18:36:00
categories: 其他
description: 在这篇记录中，我们将探讨如何破解office。阅读时长：4min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20231207132945.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将探讨如何破解office。仅用于学习，请勿用于非法用途！

### 原理

Key Management Service(简称:KMS)KMS激活会维持180天，原理：由于Windows VL都是为批量激活而诞生，所以在一个激活单位中肯定会有很多台配置相等的计算机，并用一个服务器建立起一个局域网(LAN)，而KMS正好利用这一点，它要求局域网中必须有一台KMS服务器，KMS服务器的作用是给局域网中的所有计算机的操作系统定周期(一般是180天)提供一个随机的激活ID(不同于产品激活密钥)，然后计算机里面的KMS服务就会自动将系统激活，实现正常的系统软件服务与操作。所以计算机必须保持与KMS服务器的定期连接，以便KMS激活服务的自动检查实现激活的自动续期，这样就实现了限制于公司域内的激活范围，避免了对于外界计算机的非法授权，当非法激活者离开公司域后，由于客户端KMS服务不能连接位于域内的KMS激活服务器，让它提供一个新的序列号，超过180天以后就会因为激活ID过期而重新回到试用版本状态，而合法授权者则能够定期获得ID更新，保持一直正确的激活状态。



### 步骤

1.官网下载 Office Deployment Tool https://www.microsoft.com/en-us/download/details.aspx?id=49117
2.配置config文件 https://config.office.com/deploymentsettings ，版本推荐2021标准版，然后根据需要选择软件，并导出 xml 

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240713184528.png)3.安装 

```shell
cd c:\office（你安装文件夹目录） setup.exe /download config.xml setup.exe /configure config.xml 
```

4.激活 

```shell
cd C:\Program Files\Microsoft Office\Office16 
cscript ospp.vbs /sethst:kms.03k.org （当前kms地址失效的话，可以尝试替换备选地址）
cscript ospp.vbs /act 
```

![image-20240713184734861](C:\Users\coderpeng\AppData\Roaming\Typora\typora-user-images\image-20240713184734861.png)

5.备选的KMS kms.03k.org kms.chinancce.com kms.luody.info kms.lotro.cc kms.luochenzhimu.com kms8.MSGuides.com kms9.MSGuides.com





### 总结

原理就是通过cscript（Windows Script Host）将ospp.vbs（管理 Office 产品的激活的脚步文件）中的kms服务器地址参数改为指定地址。然后通过/act参数执行激活程序。注意，此方法仅用于学习，请勿用于非法用途！

放张成功后的截图：

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/70ac79602bf4a5f0e631ad29a140d43.png)