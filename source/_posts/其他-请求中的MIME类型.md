---
title: 请求中的MIME类型
date: 2024-07-25 15:03:00
tags: 脚手架
categories: 其他
description: 在这篇记录中，我们将探讨一下请求中的MIME类型。阅读时长：4min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/drink-3025022_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将探讨一下请求中的MIME类型。

大家在平常与后端同事的联调过程中一定遇到过明明接口一字不差，类似也是一样的，可就是跑不通，这种情况通常我们会先检查传参是否缺少必填项或者格式问题，然后检查网络和代理，如果还是有问题，我们可能就会陷入僵局，比如下面这种情况：

本地环境（代理正确）

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240725150808.png)

服务器直连

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240725150733.png)

那么问题出在哪呢？其实是因为请求载体类型有问题，后端同事没有用axios默认的JSON格式为请求载体类型，而是用的`application/x-www-form-urlencoded`类型，这种类型常用于 HTML 表单数据提交，数据被编码成 `key1=value1&key2=value2` 的格式，特殊字符被 URL 编码，比如空格变成 `+` 号，特殊字符变成 `%xx` 格式。

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20240725151146.png)

知道了上面那个问题的原因后，我们再一起了解一下其他的MIME类型吧。

1. `text/plain`

- **用途**：用于纯文本数据，没有特殊格式或编码。
- **示例**：文件扩展名 `.txt`

2. `text/html`

- **用途**：用于 HTML 文档。
- **示例**：文件扩展名 `.html`

3. `application/json`

- **用途**：用于 JSON 格式的数据，通常用于 AJAX 请求和响应。
- **示例**：文件扩展名 `.json`

4. `application/xml`

- **用途**：用于 XML 数据。
- **示例**：文件扩展名 `.xml`

5. `application/javascript`

- **用途**：用于 JavaScript 代码。
- **示例**：文件扩展名 `.js`

6. `text/css`

- **用途**：用于 CSS 样式表。
- **示例**：文件扩展名 `.css`

7. `application/octet-stream`

- **用途**：用于二进制数据，可以用于任意文件下载。
- **示例**：通常用于文件下载，扩展名不限。

8. `multipart/form-data`

- **用途**：用于提交包含文件上传的表单数据。与`application/x-www-form-urlencoded`的区别是该表单数据会被分割成多个部分，每部分都有自己的 Content-Disposition 和 Content-Type，每个部分包含一个表单字段的数据，文件数据可以直接作为其中的一部分发送。
- **示例**：在 HTML 表单中使用 `enctype="multipart/form-data"`。

9. `image/jpeg`, `image/png`, `image/gif`

- **用途**：用于图像数据。
- **示例**：文件扩展名 `.jpeg`、`.jpg`、`.png`、`.gif`

10. `audio/mpeg`, `audio/ogg`

- **用途**：用于音频文件。
- **示例**：文件扩展名 `.mp3`、`.ogg`

11. `video/mp4`, `video/ogg`

- **用途**：用于视频文件。
- **示例**：文件扩展名 `.mp4`、`.ogv`

12. `application/pdf`

- **用途**：用于 PDF 文档。
- **示例**：文件扩展名 `.pdf`

13. `application/zip`, `application/gzip`

- **用途**：用于压缩文件。
- **示例**：文件扩展名 `.zip`、`.gz`

14. `text/csv`

- **用途**：用于 CSV 格式的数据。
- **示例**：文件扩展名 `.csv`



那如何将js类型转换成`application/x-www-form-urlencoded`类型呢？我们可以使用qs库，或者自己写个工具函数：

```js
import axios from 'axios';
import qs from 'qs';

// 创建 Axios 实例
const instance = axios.create({
  baseURL: 'https://your-api-base-url.com',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded'
  },
  transformRequest: [(data) => {
    return qs.stringify(data);
  }]
});

// 使用 Axios 实例发送请求
instance.post('/api/endpoint', {
  key1: 'value1',
  key2: 'value2'
})
.then(response => {
  console.log(response.data);
})
.catch(error => {
  console.error(error);
});
```

`qs` 库可以将 JavaScript 对象序列化为 URL 编码的字符串格式，也可以手写工具函数实现这一目的：

```js
import axios from 'axios';

// 手写序列化函数
function serialize(obj) {
  const str = [];
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      str.push(encodeURIComponent(key) + "=" + encodeURIComponent(obj[key]));
    }
  }
  return str.join("&");
}

// 创建 Axios 实例
const instance = axios.create({
  baseURL: 'https://your-api-base-url.com',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded'
  },
  transformRequest: [(data) => {
    return serialize(data);
  }]
});
```