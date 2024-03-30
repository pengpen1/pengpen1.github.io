---
title: 理解git中的合并操作
date: 2023-12-16 15:33:00
tags: git
categories: git
description: 记录笔者对git merge和git pull的理解。阅读时长：4min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20231207132853.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：** 在工作中我们经常使用`git merge` 或者`git pull`，但是我们对于其中的原理还不是很清楚，比如我刚学的时候，经常会像，合这个分支会不会给我增加很多不要的文件，为什么合并了没有文件改变呢？等等这些疑问，所以这里一起来学习下吧。



### 快进

我之前遇到个这么一个问题，初始有个分支1，然后在分支1的基础上新增了分支2，分支删除了某些文件，并在此基础上新增分支3 。分支3修改了某些文件后想要回之前分支2删除的文件，就想通过`git pull`分支3获取这些文件。但是提示拉取成功，但是什么都没有改变。然后就想不通，这些不是差异吗？为什么没有呢？这就是我对git的合并有认知错误，我**`把合并想成了简单的复制粘贴，有不同文件或者代码就复制过来`**。其实，合并是根据指针来的，我上面这种情况是正常的，因为分支1在分支3的上游，git合并这两个分支时，不会做任何操作。跟这个类似的情况还有快进，我们一起来看看吧。

这里，我用[Learn Git Branching](https://learngitbranching.js.org/?locale=zh_CN&NODEMO=)来演示一下，这里我把上面的分支1，2，3简化成两个分支。第一个分支main，假设上面有很多文件，我在提交2也就是c2的时候删了部分文件，之后创建并切换到dev分支，然后假设我们进行开发并提交一次到c3。

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20231218101440.png)

这时候，我想把之前删的文件在弄到dev分支上，就输入`git merge main`，结果没有任何变化。因为dev分支的上流就是main分支，所以合并不会有任何变化。这里插一嘴，想恢复的话：

```shell
//dev分支上使用 git checkout main -- <file1> <file2> ...  
//其中 <file1> <file2> ... 是你要恢复的文件路径列表。你可以列出所有被删除的文件的路径，用空格分隔。

//这将从main分支中恢复指定的文件，并将它们添加到当前的dev分支,不会自动提交需要自己提交
git checkout main -- file1.txt file2.txt

```



接着上面的话题，如果main分支合并到dev分支不会有任何变化，那dev分支合并到main分支呢？我们切换到main分支并输入`git merge dev`，会发现main分支的指针移动到了c3，其他没有任何变化，这种行为称为**快速前进，也就是快进**，当你试图合并两个分支时，如果顺着一个分支走下去能够到达另一个分支，那么 Git 在合并两者的时候，只会简单的将指针向前推进。

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20231218104555.png)



### 非快进

上面这是dev有提交，那假设main分支也有提交呢？

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20231218105235.png)

我们把main分支合并到dev分支上：`git merge main`，我们会发现自动出现一个c6分支，且这个分支同时指向c4分支和c5分支，这就是代表合并成功了。合并过程中没有冲突产生，Git会尝试自动完成合并操作，并且在合并完成后会生成一个新的提交。

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20231218105451.png)



那如果有冲突呢？GIT也会最大可能的进行合并，任何因包含合并冲突而有待解决的文件，都会以未合并状态标识出来，且不会产生合并提交，需要你手动解决冲突，再进行提交。



**## 参考链接**

- [Git：合并分支----git merge命令应用的三种情景-CSDN博客](https://blog.csdn.net/qq_42780289/article/details/97945300)