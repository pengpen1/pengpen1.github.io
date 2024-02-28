---
title: 迭代器与async函数
date: 2023-12-14 10:23:00
tags: 迭代器
categories: js
description: 探讨Async/Await深层次的原理。阅读时长：10min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/20231207132846.png
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：** 在工作中我们经常使用Async/Await 函数，我们也知道它是由Promise 和 Generator 来实现的，但是内部具体的原理我们少有涉猎，今天来一起学习下吧。

### Generator 

这是[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Iterators_and_generators)上的示例，**调用生成器函数会返回一个生成器对象，每次调用生成器对象的 next 方法会执行函数到下一次 yield 关键字停止执行，并且返回一个 { value: Value, done: boolean }的对象，如果已经迭代到序列中的最后一个值，则done为 `true`。**

- yield 关键字会停止函数执行并将 yield 后的值返回作为本次调用 next 函数的 value 进行返回。
- 同时，如果本次调用 g.next() 导致生成器函数执行完毕，那么此时 done 会变成 true 表示该函数执行完毕，反之则为 false 。

```js
function* generator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = generator(); // "Generator { }"

console.log(gen.next().value); // 1 { value: 1, done: false }
console.log(gen.next().value); // 2
console.log(gen.next().value); // 3
```

区分可迭代协议（实现@@iterato属性，返回自身则只能迭代一次，返回新的迭代器则可以多次迭代），迭代器协议（实现了一个拥有以下语义（semantic）的 **next()** 方法，一个对象才能成为迭代器），迭代器（使用 `next()` 方法实现了[迭代器协议](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#迭代器协议)的任何一个对象），生成器函数（Generator 函数，使用 [`function*`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/function*) 语法编写，调用返回生成器）。

下面是next传参和生成器函数return的用法，**next 传递值进行调用时，传入的值会被当作上一次生成器函数暂停时 yield 关键字的返回值处理。return会终止当前生成器函数的执行，并将return的值作为本次调用 next 函数的 value 进行返回。**

```js
function* gen() {
  const a = yield 1;
  console.log(a, 'this is a');
  const b = yield 2;
  console.log(b, 'this is b');
  const c = yield 3;
  console.log(c, 'this is c');
  return 'resultValue'
}

let g = gen();

g.next(); // { value: 1, done: false }
g.next('param-a'); // { value: 2, done: false }
g.next('param-b') // { value: 3, done: false }
g.next() // { value: 'resultValue', done: true }
g.next() // { value: undefined, done: true }
```

**阶段总结：**

- yield 关键字会停止函数执行并将 yield 后的值返回作为本次调用 next 函数的 value 进行返回。
- return关键字会终止当前生成器函数的执行，并将return的值作为本次调用 next 函数的 value 进行返回。
- next 传递值进行调用时，传入的值会被当作上一次生成器函数暂停时 yield 关键字的返回值处理，第一次不行，因为前面没有yield关键字，但是可以在生成器函数那设置参数作为第一次的传参。



Babel l 在低版本浏览器下为我们实现的 Generator 生成器函数的 **polyfill** 实现，下面是转换前的代码：

```js
function* gen(oneParam) {
  console.log(oneParam, "this is oneParam");
  const a = yield 1;
  console.log(a, 'this is a');
  const b = yield 2;
  console.log(b, 'this is b');
  const c = yield 3;
  console.log(c, 'this is c');
}

let g = gen("param-one");

g.next(); // { value: 1, done: false }
g.next('param-a'); // { value: 2, done: false }
g.next('param-b') // { value: 3, done: false }
g.next('param-c') // { value: undefined, done: true }
```

下面是regenerator-runtime简易版的实现和上面代码的polyfill实现，_marked是对于编译后的生成器函数作为继承使用的一个参数，并不影响函数的核心逻辑，所以我们暂时忽略它。

```js
// 下面是regenerator-runtime简易版的实现
// require("regenerator-runtime/runtime.js");

const regeneratorRuntime = {
  // 存在mark方法，接受传入的fn。原封不懂的返回fn
  mark(fn) {
    return fn;
  },
  wrap(fn) {
    const _context = {
      next: 0, // 表示下一次执行生成器函数状态机switch中的下标
      sent: "", // 表示next调用时候传入的值 作为上一次yield返回值
      done: false, // 是否完成
      // 完成函数
      stop() {
        this.done = true;
      },
    };
    return {
      next(param) {
        // 1. 修改上一次yield返回值为context.sent
        _context.sent = param;
        // 2.执行函数 获得本次返回值
        const value = fn(_context);
        // 3. 返回
        return {
          done: _context.done,
          value,
        };
      },
    };
  },
};
var _marked = /*#__PURE__*/ regeneratorRuntime.mark(gen); // /*#__PURE__*/用于告诉代码压缩工具或编译器，某个表达式是纯粹的，不会产生副作用

function gen(oneParam) {
  var a, b, c;
  return regeneratorRuntime.wrap(function gen$(_context) {
    while (1) {
      switch ((_context.prev = _context.next)) {
        case 0:
          console.log(oneParam, "this is oneParam");
          _context.next = 2;
          return 1;

        case 2:
          a = _context.sent;
          console.log(a, "this is a");
          _context.next = 6;
          return 2;

        case 6:
          b = _context.sent;
          console.log(b, "this is b");
          _context.next = 10;
          return 3;

        case 10:
          c = _context.sent;
          console.log(c, "this is c");

        case 12:
        case "end":
          // 利用不写break和return造成的穿透执行效果实现终止
          return _context.stop();
      }
    }
  }, _marked);
}

var g = gen("param-one");
console.log(g.next());
console.log(g.next("param-a"));
console.log(g.next("param-b"));
console.log(g.next("param-c"));
```

**内部核心思想本质上就是通过 regeneratorRuntime.wrap 函数包裹一个状态机函数 fn 。wrap 函数内部维护一个 _context 对象，从而每次调用返回的生成器对象的 next 方法时，被包裹的状态机函数根据 _context 的对应属性匹配对应状态来完成不同的逻辑。**



### 模拟Async函数

上面我们学习了生成器函数和迭代器相关的知识，现在我们来看一段代码：

```js
function promise1() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve("1");
    }, 1000);
  });
}

function promise2(value) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve("value:" + value);
    }, 1000);
  });
}

function* readFile() {
  const value = yield promise1();
  console.log(value);
  const result = yield promise2(value);
  console.log(result);
  return result;
}

const testAsync = async () => {
  const value = await promise1();
  console.log(value);
  const result = await promise2(value);
  console.log(result);
  return result;
}
```

是不是和async函数很像，async函数的特点就是返回值是一个promise，内部可以使用`await` 关键字实现异步行为。那我们试试用生成器函数模拟async函数：

```js
// 思考如何用生成器函数实现async的功能，有结果在执行下一个，且返回的是promise
function asyncReadFile() {
    const generator = readFile();
    return new Promise((resolve, reject) =>{
        const nextPromise = generator.next();
        nextPromise.value.then((res)=>{
            const nextPromise = generator.next(res);
            nextPromise.value.then((res)=>{
                generator.next(res);
                resolve(res);
            })
        })
    })
}
asyncReadFile().then(res=>console.log(res));
```

但是上面这种写法没有通用性，只适合上面这种情况，如果在增加几个await，又要嵌套几层then函数，且不支持非promise情况，如yield 88，虽然也可以实现，但是通用性不高。

```js
function asyncReadFile() {
  const generator = readFile();
  return new Promise((resolve, reject) => {
    const nextPromise = generator.next();
    nextPromise.value.then((res) => {
      // promise1的结果
      const nextObject = generator.next(res); //给value赋值
      // nextObject.value 是88
      // p
     const nextPromise = generator.next(nextObject.value);

      nextPromise.value.then((res) => {
        generator.next(res);
        resolve(res);
      });
    });
  });
}
```

要实现通用性，首先想到的就是递归处理，利用done来判断是否结束递归，内部判断value是不是promsie，不是直接递归该值，是的话用then处理一下。next函数需要一个参数，因为使用 Generator 来处理异步问题时，通过 `const a = yield promise` 将 promise 的 resolve 值交给 a ，所以我们需要在每次 `then` 函数中将 res 传递给下一次的 next(res) 作为上次 yield 的返回值。

```js
function co(generator) {
  return new Promise((resolve, reject) => {
    const iterator = generator();

    const next = (params) => {
      const { value, done } = iterator.next(params);
      if (done) {
        resolve(value);
      } else {
        // 模拟await以及处理非promise的特殊情况
        if (value instanceof Promise) {
          value
            .then((res) => {
              next(res);
            })
            .catch((err) => reject(err));
          // 异步rejected，不能用外面的try catch来处理，会报 ERR_UNHANDLED_REJECTIO
        } else {
          next(value);
        }
      }
    };

    next();
  });
}
co(readFile)
  .then((res) => console.log(res))
  .catch((err) => console.log("111", err));
// catch也能捕获程序运行错误
```

除了用instanceof Promise来判断是否是promise外，还有个更巧妙的处理方法，利用Promise的静态方法包裹一下，这在不清楚一个值是否是 Promise时，最好用的处理方法。

```js
Promise.resolve(value).then((res) => next(res));
```

至此我们可以得出，**Async 就是将 Generator 包裹了一层 co 函数，所以它被称为 Generator 和 Promise 的语法糖。**



### 结论

本篇文章讲述了 Generator 函数的特性以及在低版本浏览器上的polyfill实现，到最后的手写co函数，理解Async语法糖的核心原理。



### 参考链接

Async是如何被JavaScript实现的 - WangHaoyu的文章 - 知乎
https://zhuanlan.zhihu.com/p/473245486