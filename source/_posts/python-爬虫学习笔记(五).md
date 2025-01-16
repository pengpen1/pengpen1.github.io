---
title: 爬虫学习笔记(五)
date: 2025-01-16 15:46:00
tags: python
categories: python
description: 笔者学习python爬虫的第五篇笔记，主要内容是用 Venv 安装虚拟环境，并执行一个获取图片的爬虫代码。阅读时长：7min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/823784d8f7ca7826ea585fcf0e12920.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将用 Venv 安装虚拟环境，并执行一个获取图片的爬虫代码，效果如下所示

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/823784d8f7ca7826ea585fcf0e12920.png)

### 安装virtualenv

```shell
pip install virtualenv
```



### 创建虚拟环境

创建一个新的项目文件夹，在终端中使用 cd 切换到项目文件夹，并运行以下命令：

```shell
 python -m venv <virtual-environment-name>
 
 // 比如
 mkdir projectA
 cd projectA
 python -m venv my_env
```

我用的Windows成功后在文件夹下出现my_env文件夹，里面有Lib，Scripts等



激活你的虚拟环境，运行下面的代码：

```shell
my_env\Scripts\activate
```

成功的话会命令行前面会出现（环境名）前缀



关闭环境，输入以下命令即可：

```shell
deactivate
```

vscode默认设置了，打开终端自动激活虚拟环境，可以在设置中搜索python.terminal.activateEnvironment，查看是否开启了这个配置



### 编写爬虫文件

我这里用的l站佬友分享的爬取头像的代码：

```python
import requests
from bs4 import BeautifulSoup
import os
import threading

# 下载单张图片
def download_image(img_url, save_dir):
    try:
        img_data = requests.get(img_url, timeout=10).content
        img_name = os.path.join(save_dir, img_url.split("/")[-1])
        with open(img_name, "wb") as f:
            f.write(img_data)
        print(f"下载成功: {img_url} -> {img_name}")
    except Exception as e:
        print(f"下载失败: {img_url}, 错误信息: {e}")

# 爬取详情页中的图片
def scrape_detail_page(detail_url, save_dir):
    try:
        print(f"正在爬取详情页: {detail_url}")
        response = requests.get(detail_url, timeout=10)
        if response.status_code != 200:
            print(f"无法访问详情页: {detail_url}, 状态码: {response.status_code}")
            return

        soup = BeautifulSoup(response.content, "html.parser")
        img_tags = soup.select("#content p img")  # 选择详情页中的图片标签

        for img_tag in img_tags:
            img_url = img_tag["src"]
            download_image(img_url, save_dir)
    except Exception as e:
        print(f"爬取详情页失败: {detail_url}, 错误信息: {e}")

# 爬取主页面，获取所有详情页链接
def scrape_images(base_url, save_dir, thread_count):
    if not os.path.exists(save_dir):
        os.makedirs(save_dir)

    page = 1
    threads = []

    while True:
        try:
            # 生成分页 URL
            url = base_url.replace("_1", f"_{page}")
            print(f"正在爬取页面: {url}")
            response = requests.get(url, timeout=10)

            if response.status_code != 200:
                print(f"无法访问页面: {url}, 状态码: {response.status_code}")
                break

            soup = BeautifulSoup(response.content, "html.parser")
            detail_links = soup.select("ul.g-gxlist-imgbox li a")

            if not detail_links:
                print("未找到更多详情页链接，爬取结束。")
                break

            for link in detail_links:
                detail_url = "https://www.qqtn.com" + link["href"]

                # 创建线程爬取详情页
                thread = threading.Thread(target=scrape_detail_page, args=(detail_url, save_dir))
                threads.append(thread)
                thread.start()

                # 控制线程数量
                while len(threads) >= thread_count:
                    for t in threads:
                        t.join(0.1)
                    threads = [t for t in threads if t.is_alive()]

            page += 1
        except Exception as e:
            print(f"爬取页面失败: {url}, 错误信息: {e}")
            continue

    # 等待所有线程结束
    for t in threads:
        t.join()

# 示例用法
base_url = "https://www.qqtn.com/tx/nvshengtx_1.html"
save_directory = "qqtn_images"
thread_count = 5  # 可调整线程数量
scrape_images(base_url, save_directory, thread_count)
```

文件名随意，后缀是py就行，需要放到根目录下，也就是和环境文件夹同级



### 执行爬虫

先确保命令行cd到保存 Python 文件的目录下，也就是根目录下，然后执行

```
python 文件名.py
```

退出的话，`ctrl + c`或者直接关闭程序

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20250116133603351.png)

最终我们会在根目录下看到`save_directory`那传的目录名相同的文件夹，里面就包含了我们爬虫后得到的所有头像



### 疑问解答

**1.Python 中的JSDoc 注释**

```python
def add(x, y):
    """Adds two numbers.

    Args:
        x: The first number.
        y: The second number.

    Returns:
        The sum of x and y.
    """
    return x + y
```

文档字符串是放在函数、类或模块定义的第一行的一个字符串，用三个引号 (''' 或 """ ) 括起来。





### 参考链接

[linuxDo](https://linux.do/t/topic/359119)

