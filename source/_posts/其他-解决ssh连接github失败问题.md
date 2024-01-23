---
title: 解决ssh连接github失败问题
date: 2024-01-19 16:27:00
tags: github
categories: 其他
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20231207132945.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将探讨在使用ssh关联自己账户后，连接失败问题。执行”ssh -T git@github.com” 命令出现`ssh: connect to host github.com port 22: Connection timed out`报错的解决方案。

### 重新绑定ssh

我们先排除其他因素，确定的ssh密钥是正确绑定到你的github账户的：

所以得删除旧的：

```shell
cd ~/.ssh
ls
rm id_rsa id_rsa.pub
```

生成并复制新的密钥到你是github上：

```
ssh-keygen -t rsa -C "1592193136@qq.com"
cat ~/.ssh/id_rsa.pub
```

测试连接：

```shell
ssh -T git@github.com
```

如果出现和我上面一样报错的话，那么我们进入下一步吧。



### 更换端口以解决问题

这个问题的原因是网络或防火墙限制了你访问GitHub的22端口，github默认开放的就是22端口，所以我们需要通过在.ssh下添加配置文件将22端口改为443端口。

先进入，并新建config文件

```
cd ~/.ssh
vim config
```

然后将下面的内容复制进去：

```shell
Host github.com
User 注册github的邮箱
Hostname ssh.github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa
Port 443
```

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240119165349.png)

在英文状态下输入`:wq `推出vim编辑。简单来说就是配置了邮箱地址，私钥文件的路径，端口号。

然后在用`ssh -T git@github.com`试试吧。



### 其他方案

如果更换端口号也不行的话，可以试试刷新本地DNS缓存，可能是DNS解析出问题了：

```shell
ipconfig /flushdns
```

还不行，那何必执着于ssh，换https连接吧：

```shell
git remote set-url origin 远程仓库地址
```

​	