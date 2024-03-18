---
title: 爬虫学习笔记(四)
date: 2024-03-18 10:47:00
tags: python
categories: python
description: 笔者学习python爬虫的第四篇笔记，主要内容是介绍python中的列表、元组、字典、集合等等。阅读时长：7min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/bible-2110439_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将学习python中的列表、元组、字典、集合等等内容。

### 列表

Python中的列表基本和JavaScrip中的数组类似，不过是语法和一些内置方法有所不同罢了，所谓一通万通。大家也没必要所有的内置方法和操作符都掌握，记住常用的就行，其他的简单了解即可，之后要用到再去查。

对一个数据结构，最基本的操作就是对元素的增删改查，python中创建列表还是和js一样，方括号包裹列表元素，多个元素使用逗号分隔，元素允许使用不同的类型。

python中列表和js中数组之间差距比较大的就是操作符和删除了，python使用del会改变列表长度，而JavaScrip使用delete不会改变数组长度。操作符就比较逆天了，直接看下面的演示代码吧。

```Python
import operator

lista = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
# 增
listb = lista + [11, 12, 13] # 组合,不改变原列表,返回新列表
listb2 = listb.append(14) # 附加,改变原列表,返回None
listb3 = listb.extend([15, 16]) # 新列表追加到原来的列表,改变原列表,返回None
listb4 = lista * 2 # 重复,不改变原列表,返回新列表
# 删
del lista[1]
# 改
lista[0] = 0
# 查
is2 =  2 in lista

print(lista) # output: [0, 3, 4, 5, 6, 7, 8, 9, 10]
print(listb) # output: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16]
print(listb2) # output: None
print(listb3) # output: None
print(listb4) # output: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
print(is2) # output: False

# 列表截取
liste = lista[5:]
# 列表推导式（List Comprehension）筛选出所有满足条件的偶数
listf = [x for x in lista if x % 2 == 0]
# 获取长度
len = len(lista)
# 列表比较
# output: False
print(operator.eq(lista, listb))
# output: True
print(operator.eq([123], [123]))


# output: [6, 7, 8, 9, 10]
print(liste)
# output: [2, 4, 6, 8, 10]
print(listf)
# output: 9
print(len)

```

为了防止Python的语法影响到JavaScrip的基础，这里我们在简单复习一下：

```js
const array = [1, 2, 3, 4, 5];
const list = array + [123];
// + 运算符的任一操作数不是数字或字符串，JavaScript会尝试将其转换为相应的字符串，然后执行字符串连接操作。
console.log(list, typeof list); //1,2,3,4,5123 string
delete array[0]; // 删除对象的一个属性,对数组进行操作的话不会影响数组长度
console.log(array[0], 0 in array, array, array.length); // undefined false [ <1 empty item>, 2, 3, 4, 5 ] 5
//  push()、splice、join()、slice()、indexOf()、concat() 等
console.log(array.indexOf(2)); // 1
console.log(2 in array); // true
```

要注意的就是`+` 运算符

- 如果`+` 运算符的两个操作数都是数字，它将执行数值相加操作
- 如果 `+` 运算符的任一操作数是字符串，则它会执行字符串连接操作
- 如果 `+` 运算符的任一操作数不是数字或字符串，JavaScript会尝试将其转换为相应的字符串，然后执行字符串连接操作

js中删除数组里面的元素，我们基本不会用delete，一般用splice，或者直接赋值



### **TODO**



### 疑问解答

**1.计算机中内存、进程、线程各是什么？**

先说结论：内存是临时存储程序及其数据的地方，进程是操作系统中的一个执行实例，操作系统通过进程管理来确保多个程序能够并发执行，并且有效地利用计算机资源。而线程则是进程中的执行单元，它们共同协作以实现程序的运行。

1. **内存（RAM）：** 内存是计算机用于临时存储数据和程序的地方。它是一种易失性存储设备，这意味着当计算机关闭或断电时，存储在内存中的数据都会丢失。内存的主要目的是为了提供对数据的快速访问，因此相比于硬盘等存储设备，内存的读写速度要快得多。
2. **进程：** 进程是操作系统中的一个执行实例。当你启动一个程序时，操作系统会创建一个相应的进程来运行该程序。每个进程都拥有独立的内存空间，包括代码、数据、堆栈等。进程之间通常是相互隔离的，一个进程的数据不会直接影响到另一个进程。每个进程都有自己的资源分配和管理，包括内存、文件句柄等。
3. **线程：** 线程是进程中的一个执行单元。一个进程可以包含多个线程，这些线程共享进程的资源，如内存空间和文件句柄等。不同于进程，线程之间共享同一份内存空间，因此线程间的通信和数据共享更为方便。线程的创建和切换相对于进程来说开销较小，因此多线程编程常用于提高程序的并发性和性能。

在一个程序运行时，操作系统会为其创建一个进程，并在该进程内创建一个或多个线程，这些线程负责执行不同的任务。这些任务所需的数据会被加载到内存中，然后由对应的线程执行。当程序结束时，操作系统会释放进程及其所占用的内存空间，这些数据也会从内存中清除。





### 参考链接

- [廖雪峰/python教程](https://www.liaoxuefeng.com/wiki/1016959663602400/1017063826246112)
- [Python | 菜鸟教程 (runoob.com)](https://www.runoob.com/python3/python3-list.html)


