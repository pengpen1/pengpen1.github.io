---
title: 策略模式优化if-else
date: 2023-11-29 10:10:00
tags: 设计模式
categories: js
description: 简单的探讨多分支优化方案。阅读时长：3min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/soap-bubbles-8253276_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：** 在工作中我们经常使用if-else语句，但是如果分支比较多，用if-else就不是那么美观，所以在此介绍几种好的优化方案。

### if-else

以计算商品价格为例，要求是根据商的折扣倍率计算出最终价格，没有折扣倍率的商品则返回原价，如"A"类商品的折扣倍率为0.1，则最终价格为price - price * 0.1。下面是用if-else的写法，也是我们大部分同学都会采用的方案：

```js
function calculatePrice(product, price) {
  let discount = 0;

  if (product === "A") {
    discount = price * 0.1;
  } else if (product === "B") {
    discount = price * 0.2;
  } else if (product === "C") {
    discount = price * 0.15;
  }

  return price - discount;
}
```



### switch

switch的优点是比if-else更清晰美观，但是如果要增加或修改已有的商品折扣率，会修改原始的计算函数。

```js
// switch
function calculatePrice2(product, price) {
  let discount = 0;

  switch (product) {
    case "A":
      discount = price * 0.1;
      break;
    case "B":
      discount = price * 0.2;
      break;
    case "C":
      discount = price * 0.15;
      break;
    default:
      break;
  }

  return price - discount;
}
```



### map

利用map来存储商品折扣率，需要修改或增加时只需更改map即可，具有较高的可维护性和可扩展性。

```js
// map
const MAGNIFICATION = {
  A: 0.1,
  B: 0.2,
  C: 0.15,
};
function calculatePrice3(product, price) {
  const magnification = MAGNIFICATION[product] || 0;
  return price - price * magnification;
}
```



### 策略模式

和map方案类似，但是比map更灵活，map局限于折扣率这一个变量。而策略模式提升到了每种商品类型的折扣逻辑，具有较高的灵活性和可扩展性。

```js
// 策略模式
const discountStrategies = {
  A: (price) => price * 0.1,
  B: (price) => price * 0.2,
  C: (price) => price * 0.15,
};

function calculatePrice4(product, price) {
  const discountStrategy = discountStrategies[product] || ((price) => 0);
  return price - discountStrategy(price);
}
```



