---
layout:     post
title:      面试知识点-JavaScript
subtitle:   JS 高级、ES6 和性能方面的知识
date:       2020-07-16
author:     Lee
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 面试知识点
    - JavaScript
---

## JavaScript
### 继承
原型链继承
```JavaScript
  let obj = {};
  obj.toString();   // obj 并没有 toString() 方法，该方法继承自 obj.__proto__
```


### 原型链
所有的构造函数都是 Function 构造函数的实例，包括 Function 构造函数；所有的对象都是 Object 构造函数的实例，不包括 Object.prototype

```JavaScript
Object.__proto__ === Function.prototype
Function.__proto__ === Function.prototype
Function.prototype.__proto__ === Object.prototype
Object.prototype.__proto__ === null
```

### this 指向
 1. 全局中的 this，指向 window
 2. 全局函数中的 this，指向 window
 3. 构造函数中的 this，指向构造函数的实例
 4. 对象方法中的 this，指向调用对象方法的对象
 5. 回调函数中的 this，指向 window（如，定时器的回调函数中的 this）
 6. 元素绑定的事件回调函数中的 this，指向绑定事件的元素
 7. 使用 call、apply 方法调用函数，传入 null、undefined 或什么都不传的情况下 this 指向 window；传入 Boolean 值 this 指向 true；其他情况 this 指向传入的合法值
 8. 箭头函数中的 this，指向父级作用域中的 this


### 设计模式
 1. 工厂模式
 2. 构造函数模式
 3. 原型模式
 4. 结合构造函数与原型模式
 5. 动态原型模式


### call、apply、bind
 1. call、apply、bind 都可以改变函数的 this 指向
 2. call 与 apply 的区别：call 方法接受的是一个参数列表，apply 方法接受的是一个包含多个参数的数组
 3. bind 与 call 和 apply 的区别：call 和 apply 会直接调用函数，而 bind() 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调用时使用


### new 实现
new 关键字会进行如下操作：
1. 创建一个空对象 obj
2. 将 obj 的 \_\_proto\_\_ 指向构造函数的 prototype，并将 obj 的 \_\_proto\_\_.constructor 指向构造函数
3. 将参数传入构造函数并更改 this 指向为 obj
4. 返回 obj

```JavaScript
  function New (Fn) {
    let obj = {};
    let args = [].slice.call(arguments, 1);
    obj.__proto__ = Fn.prototype;
    obj.__proto__.constructor = Fn;
    let result = Fn.apply(obj, args);
    return result !== null && typeof result === 'object' ? result : obj;
  }
```

### 防抖节流
1. 防抖：触发事件后在 n 秒内函数只执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间。
2. 节流：连续触发事件，但是在 n 秒内只执行一次函数。


### let、var、const 的区别
1. let、const 是 ES6 语法
2. let、const 不允许重复定义
3. let、const 存在块级作用域
4. let、const 不存在变量提升
5. let、const 存在暂时性死区
6. 全局 let、const 不会变为 window 对象的属性
7. const 的值不允许修改，并且必须在声明的时候赋值


### 暂时性死区
在 ECMAScript 6 中，let（const）将不会提升变量到代码块的顶部。因此在变量声明之前引用这个变量，将抛出引用错误（ReferenceError）。这个变量将从代码块开始时就处在一个“暂时性死区”，直到这个变量被声明为止。

```JavaScript
  console.log(x);
  let x = 3;
```

### event loop
```JavaScript
  setTimeout(function () {
    console.log(1);
  }, 0)
  new Promise(function (resolve, reject) {
    console.log(2);
    for (var i = 0; i < 10; i++) {
      i === 9 && resolve();
    }
    console.log(3);
  }).then(function () {
    console.log(4);
  })
  console.log(5);
```

输出顺序是 2、3、5、4、1

1. js 由上到下解析，依次输出 2、3、5
2. 4 在 5 后面输出是因为它是处于 resolve 的异步回调中，所以会先挂到任务队列里
3. 1 在 4 后面输出是因为 setTimeout 回调属于宏任务，而 promise 回调属于微任务，在执行一个宏任务之前会先清空所有的微任务，所以先输出 1

### Promise
#### 1. Promise 的含义
Promise 是异步编程的一种解决方案。从语法上讲，Promise 是一个对象，从它可以获取异步操作的消息，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。

Promise 对象有以下两个特点：

1. 对象的状态不受外界影响。Promise 对象代表一个异步操作，有三种状态：pending（进行中）、fulfilled（已成功）和 rejected（已失败）。只有异步操作的结果才可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。

2. 一旦状态改变就不会再变，任何时候都可以得到这个结果。Promise 对象的状态改变只有两种可能：从 pending 变为 fulfilled 和从 pending 变为 rejected。如果状态改变了，你再对 Promise 对象添加回调函数，也会立即得到这个结果。

**优点**

Promise 对象可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。

**缺点**

首先，无法取消 Promise，一旦建立它就会立即执行，无法中途取消。其次，如果不设置回调函数 Promise 内部抛出的错误不会反应到外部。第三，当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

#### 2. 基本用法
ES6 规定，Promise 对象是一个构造函数，用来生成 Promise 实例。

```JavaScript
  const promise = new Promise(function(resolve, reject) {
    if (/* 异步操作成功 */){
      resolve(value);
    } else {
      reject(error);
    }
  });  
```

Promise 构造函数接受一个函数作为参数，该函数的两个参数分别是 resolve 和 reject。它们是两个函数，由 JavaScript 引擎提供，不用自己部署。

resolve 函数的作用是，将 Promise 对象的状态从 pending 变为 resolved，在异步操作成功时调用，并将异步操作的结果作为参数传递出去；reject 函数的作用是，将 Promise 对象的状态从 pending 变为 rejected，在异步操作失败时调用，并将异步操作报出的错误作为参数传递出去。

Promise 实例生成以后，可以用 then 方法分别指定 resolved 和 rejected 状态的回调函数。

```JavaScript
  promise.then(value => {
    // success
  }, error => {
    // failure
  })
```

then 方法可以接受两个回调函数作为参数。第一个回调函数是 Promise 对象的状态变为 resolved 时调用，第二个回调函数是 Promise 对象的状态变为 rejected 时调用，是可选的。这两个函数都接受 Promise 对象传出的值作为参数。

#### 3. Promise.prototype.then()
then 方法返回的是一个新的 Promise 实例。因此可以采用链式调用写法，即 then 方法后面再调用另一个 then 方法。第一个回调函数完成以后，会将返回结果作为参数，传入第二个回调函数。

```JavaScript
  getJSON('/posts.json').then(json => {
    return json.post;
  }).then(post => {
    // ...
  })
```

#### 4. Promise.prototype.catch()
Promise.prototype.catch() 方法是 .then(null, rejection) 或 .then(undefined, rejection) 的别名，用于指定发生错误时的回调函数。

```JavaScript
  getJSON('/posts.json').then(posts => {
    // ...
  }).catch(error => {
    // 处理 getJSON 和前一个回调函数运行时发生的错误
    console.log(error);
  })
```

一般来说，不要在 then 方法里面定义 Reject 状态的回调函数（即 then 的第二个参数），而是使用 catch 方法。

```JavaScript
  // bad
  promise.then(data => {
    // success
  }, error => {
    // error
  })

  // good
  promise.then(data => {
    // success
  }).catch(error => {
    // error
  })
```

上面代码中，第二种写法要好于第一种，理由是第二种方法可以捕获前面 then 方法执行中的错误，也更接近同步的写法（try/catch)。

catch 方法返回的还是一个 Promise 对象，因此后面还可以接着调用 then 方法。

```JavaScript
  promise.then(data => {
    throw new Error('出错了');
  }).catch(error => {
    console.log(error);
    return 'hello world';
  }).then(data => {
    console.log(data);
  })
  // Error：出错了
  // hello world
```

#### 5. Promise.prototype.finally()
finally 方法用于指定不管 Promise 对象最后的状态如何，都会执行的操作。该方法是 ES2018 引入标准的。

```JavaScript
  promise
  .then(data => {})
  .catch(error => {})
  .finally(() => {})
```

finally 方法的回调函数不接受任何参数，没法知道前面的 Promise 状态到底是什 resolved 还是 rejected。finally 方法总是返回原来的值。

```JavaScript
  Promise.resolve(2).then(data => {}).then(console.log);
  // undefined
  Promise.resolve(2).finally(() => {}).then(console.log);
  // 2
  Promise.reject(3).catch(error => {}).then(console.log);
  // undefined
  Promise.reject(3).finally(() => {}).catch(console.log);
  // 3
```

#### 6. Promise.all()
Promise.all() 方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

```JavaScript
  const promises = Promise.all([p1, p2, p3]);
  promises
  .then(datas => {})
  .catch(error => {})
```

上面代码中，Promise.all() 方法接受一个数组作为参数，p1、p2、p3 都是 Promise 实例，如果不是就会调用 Promise.resolve() 方法，将参数转为 Promise 实例，再进一步处理。另外，Promise.all() 方法的参数可以不是数组，但必须具有 Iterator 接口，并返回的每个成员都是 Promise 实例。

p 的状态由 p1、p2、p3 决定，分两种情况：

1\. 只有 p1、p2、p3 的状态都变成 fulfilled，p 的状态才会变成 fulfilled，此时 p1、p2、p3 的返回值组成一个数组，传递给 p 的回调函数。

2\. 只要 p1、p2、p3 中有一个被 rejected，p 的状态就变成 rejected，此时第一个 rejected 的实例的返回值，会传递给 p 的回调函数。

**注意**：如果作为参数的 Promise 实例，自己定义了 catch 方法，那么它一旦被 rejected，并不会触发 Promise.all() 的 catch 方法。而是触发自己定义的 catch 方法，然后返回一个新的 resolved 的 Promise 实例。

#### 7. Promise.race()
Promise.race() 方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。大致与 Promise.all() 方法相同，只是如果有一个参数率先改变了状态，那么它的状态就跟着改变。那个率先改变状态的 Promise 实例的返回值，就传递给它的回调函数。

下面的例子中，如果指定时间内没有获得结果，就将 Promise 的状态变为 reject，否则变为 resolve。

```JavaScript
  const promises = Promise.race([
    fetch('/resource-that-may-take-a-while'),
    new Promise((resolve, reject) => {
      setTimeout(() => reject(new Error('request timeout')), 5000);
    })
  ])
  promises
  .then(console.log)
  .catch(console.error);
```

#### 8. Promise.allSettled()
Promise.allSettled() 方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例。只有等到这些参数实例都返回结果（不管是 fulfilled 还是 rejected），包装实例才会结束。该方法由 ES2020 引入。

该方法返回的新的 Promise 实例，一旦结束，状态总是 fulfilled，不会变成 rejected。状态变成 fulfilled 后，Promise 的监听函数接收到的参数是一个数组，每个成员对应一个传入 Promise.allSettled() 的 Promise 实例。

```JavaScript
  const promise = Promise.allSettled([Promise.resolve(1), Promise.reject(2)]);
  promise.then(console.log);
  // [
  //   {status: 'fulfilled', value: 1},
  //   {status: 'rejected', value: 2}
  // ]
```

#### 9. Promise.resolve()
Promise.resolve() 方法可以将现有的对象转换为 Promise 对象。

```JavaScript
  Promise.resolve('foo');
  // 等价于
  new Promise(resolve => resolve('foo'));
```

Promise.resolve() 方法的参数分成四中情况：

**1. 参数是一个 Promise 实例**

如果参数是 Promise 实例，那么 Promise.resolve() 将不做任何修改，原封不动地返回这个实例。

**2. 参数是一个 thenable 对象**

Promise.resolve() 方法会将 thenable 对象转换为 Promise 对象，然后就立即执行 thenable 对象的 then 方法。

```JavaScript
  let thenable = {
    then: (resolve, reject) => resolve('hello world')
  }
  Promise.resolve(thenable).then(console.log)
  // hello world
```

**3. 参数不是具有 then 方法的对象，或根本就不是对象**

如果参数是一个原始值，或者是一个不具有 then 方法的对象，则 Promise.resolve() 方法返回一个新的 Promise 对象，状态为 resolved，并且将参数传给回调函数。

```JavaScript
  Promise.resolve('hello world').then(console.log);
  // hello world
```

**4. 不带任何参数**

Promise.resolve() 方法允许调用时不带任何参数，直接返回一个 resolved 状态的 Promise 对象。

需要注意的是，立即 resolve() 的 Promise 对象是在本轮“事件循环”结束时执行的，而不是下一轮“事件循环”的开始执行的。

```JavaScript
  setTimeout(function () {
    console.log(1);
  }, 0);
  Promise.resolve().then(console.log);
  console.log(3);
  // 上面代码输出顺序：3、undefined、1
```

#### 10. Promise.reject()
Promise.reject() 方法会返回一个新的 Promise 实例，该实例的状态为 rejected。

```JavaScript
  Promise.reject('出错了').catch(console.log);
  // 等同于
  new Promise((resolve, reject) => reject('出错了')).catch(console.log);
```

注意，Promise.reject() 方法的参数，会原封不动地作为 reject 的理由，变成后续方法的参数。这一点与 Promise.resolve() 方法不一致。

```JavaScript
  const foo = function () {}
  Promise.reject(foo).catch(e => {
    console.log(e === foo);
    // true
  })
```

### async 和 await
#### 1. 含义
ES2017 标准引入了 async 函数，使得异步操作更加方便。async 函数是 Generator 函数的语法糖，async 函数将 Generator 函数的星号（*）替换成 async，将 yield 替换成 await。

async 函数对 Generator 函数的改进体现在以下四点：

**1）内置执行器**

Generator 函数的执行必须靠执行器，需要调用 next 方法，或者使用 co 模块才能真正执行，得到最后结果。而 async 函数自带执行器，执行时跟普通函数一样只需要一行。

```JavaScript
  async function asyncGetAvatar() {
    await Promise.resolve();
  }
  asyncGetAvatar();
```

**2）更好的语义**

async 和 await 比起星号和 yield 语义更清楚，async 表示函数里有异步操作，await 表示紧跟在后面的表达式需要等待结果。

**3）更广的适用性**

co 模块约定，yield 命令后面只能是 Thunk 函数或 Promise 对象，而 async 函数的 await 命令后面可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时会自动转换成立即 resolved 的 Promise 对象）。

**4）返回值是 Promise**

async 函数的返回值是 Promise 对象，async 函数完全可以看作多个异步操作包装成的一个 Promise 对象，而 await 命令就是内部 then 命令的语法糖。

#### 2. 基本用法
async 函数返回一个 Promise 对象，可以使用 then 方法添加回调函数。当函数执行的时候，一旦遇到 await 就会先返回，等到异步操作完成再接着执行函数体后面的语句。

```JavaScript
  async foo (bar) {
    const baz = await bar();
    return baz;
  }
  foo(bar).then(result => console.log(result))
```

上面例子中，函数前面的 async 关键字表明该函数内部有异步操作。调用该函数时，会立即返回一个 Promise 对象。

```JavaScript
  function timeout (ms) {
    return new Promise(resolve => {
      setTimeout(resolve, ms);
    })
  }
  async function asyncPrint (value, ms) {
    await timeout(ms);
    console.log(value);
  }
  asyncPrint('hello world', 1000);
```

上面代码指定 1000 毫秒后输出 hello world。

由于 async 函数返回的是 Promise 对象，所以可以作为 await 命令的参数。上面的例子也可以写成下面这种形式。

```JavaScript
  async function timeout(ms) {
    await new Promise((resolve) => {
      setTimeout(resolve, ms);
    });
  }
  async function asyncPrint(value, ms) {
    await timeout(ms);
    console.log(value);
  }
  asyncPrint('hello world', 1000);
```

async 函数有多种使用形式

```JavaScript
  // 函数声明
  async function foo() {}

  // 函数表达式
  const foo = async function() {}

  // 对象的方法
  let obj = {async foo() {}};
  obj.foo().then(...)

  // Class 的方法
  class Person {
    constructor(name) {
      this.name = name;
    }
    async getInfo() {
      const info = await getUserInfo(this.name);
      return info;
    }
  }

  const man = new Person('Jack');
  man.getInfo.then(...);

  // 箭头函数
  const foo = async () => {};
```

#### 3. 语法
async 函数的语法规则总体上比较简单，难点是错误处理机制。

**返回 Promise 对象**

async 函数返回一个 Promise 对象，async 函数内部 return 语句返回的值，会成为 then 方法回调函数的参数。

```JavaScript
  async function foo() {
    return 'hello world';
  }
  foo().then(result => console.log(result))
  // hello world
```

async 函数内部抛出错误会导致返回的 Promise 对象变为 reject 状态。抛出的错误对象会被 catch 方法回调函数接收到。

```JavaScript
  async function foo() {
    throw new Error('出错了');
  }
  foo().catch(e => console.log(e));
  // Error: 出错了
```

**Promise 对象的状态变化**

async 函数返回的 Promise 对象，必须等到内部所有 await 命令后面的 Promise 对象执行完才会发生状态改变，执行 then 方法指定的回调函数。除非遇到 return 语句或者抛出错误。

```JavaScript
  async function foo() {
    await Promise.resolve(1);
    console.log(2);
    return 3;
  }
  foo().then(console.log);
  // 3
```

**await 命令**

正常情况下，await 命令后面是一个 Promise 对象，返回该对象的结果。如果不是 Promise 对象就直接返回对应的值。

```JavaScript
  async function foo() {
    // 等同于 return 123;
    return await 123;
  }
  foo().then(console.log);
  // 123
```

另一种情况是，await 命令后面是一个 thenable 对象（即定义了 then 方法的对象），那么 await 会将其等同于 Promise 对象。

```JavaScript
  class Sleep {
    constructor(timeout) {
      this.timeout = timeout;
    }
    then(resolve, reject) {
      const startTime = Date.now();
      setTimeout(() => resolve(Date.now() - startTime), this.timeout);
    }
  }
  (async () => {
    const sleepTime = await new Sleep(1000);
    console.log(sleepTime);
  })();
  // 1000
```

JavaScript 一直没有休眠的语法，但是借助 await 命令就可以让程序停顿指定的时间。

```JavaScript
  function sleep(interval) {
    return new Promise(resolve => {
      setTimeout(resolve, interval);
    })
  }

  (async function () {
    for (var i = 0; i < 5; i++) {
      console.log(i);
      await sleep(1000);
    }
  })();
```

await 命令后面的 Promise 对象如果变为 reject 状态，则 reject 的参数会被 catch 方法的回调函数接收到。

```JavaScript
  async function foo() {
    // 等同于 return await Promise.reject('出错了');
    await Promise.reject('出错了');
  }
  foo()
  .then(console.log)
  .catch(console.log);
  // 出错了
```

任何一个 await 命令后面的 Promise 对象变为 reject 状态，那么整个 async 函数都会中断执行。

```JavaScript
  async function foo() {
    await Promise.reject('出错了');
    await Promise.resolve('hello world');  // 不会执行
  }
```

**错误处理**

如果 await 后面的异步操作出错，那么等同于 async 函数返回的 Promise 对象被 reject。

```JavaScript
  async function foo() {
    await new Promise((resolve, reject) => {
      throw new Error('出错了');
    })
  }
  foo().then(
    resolve => console.log(resolve),
    error => console.log(error)
  )
  // Error: 出错了
```

防止出错的方法，是将可能出错的代码放到 try...catch 代码块中。

```JavaScript
  async function foo() {
    try {
      await new Promise((resolve, reject) => {
        throw new Error('出错了');
      })
    } catch (e) {
    }
    return await('hello world');
  }
```

另一种方法是 await 后面的 Promise 对象再跟一个 catch 方法，处理前面可能出现的错误。

```JavaScript
  async function foo() {
    await Promise.reject('出错了')
    .catch(e => console.log(e));
    return await('hello world');
  }
```

如果有多个 await 命令，可以统一放在 try...catch 结构中。

```JavaScript
  async function foo() {
    try {
      await bar()
      await baz()
      await 3
    } catch (e) {
      console.log(e);
    }
  }
```

**使用注意点**

第一点：await 命令后面的 Promise 对象的运行结果可能是 rejected，所以最好是把 await 命令放在 try...catch 代码块中。

第二点：多个 await 命令后面的异步操作如果不存在继发关系，最好让它们同时触发，这样会缩短程序的执行时间。

```JavaScript
  // 写法一
  let [foo, bar] = await Promise.all([getFoo(), getBar()]);

  // 写法二
  let fooPromise = getFoo();
  let barPromise = getBar();
  let foo = await fooPromise;
  let bar = await barPromise;
```

如果确实希望多个请求并行执行，可以使用 Promise.all 方法。当三个请求都会 resolved 时，下面两种写法效果相同。

```JavaScript
  // 写法一
  async function foo() {
    let bars = [1, 2, 3];
    let promises = bars.map(bar => baz(bar));

    let results = await Promise.all(promises);
    console.log(results);
  }

  // 写法二
  async function foo() {
    let bars = [1, 2, 3];
    let promises = bars.map(bar => baz(bar));

    let results = [];
    for(let promise of promises) {
      results.push(await promise);
    }
    console.log(results);
  }
```

第三点：await 命令只能用在 async 函数中，如果在普通函数中使用则会报错。

第四点：async 可以保存运行堆栈。

```JavaScript
  const a = () => {
    b().then(() => c());
  };
```

上面代码中，函数 a 内部运行了一个异步任务 b。当 b 运行的时候，函数 a 不会中断，而是继续执行。等到 b 运行结束，可能 a 早就运行结束了，b 所在的上下文环境已经消失了。如果 b 或 c 报错，错误堆栈将不包括 a。

```JavaScript
  const a = async () => {
    await b();
    c();
  };
```

上面代码中，b 运行的时候，a 是暂停执行，上下文环境都保存着。一旦 b 或 c 报错，错误堆栈将包括 a。

#### 4. 实例：按顺序完成异步操作
```JavaScript
  async function logInOrder(urls) {
    // 并发读取远程URL
    const textPromises = urls.map(async url => {
      const response = await fetch(url);
      return response.text();
    });

    // 按次序输出
    for (const textPromise of textPromises) {
      console.log(await textPromise);
    }
  }
```

上面代码中，虽然 map 方法的参数是 async 函数，但它是并发执行的，因为只有 async 函数内部是继发执行，外部不受影响。后面的 for..of 循环内部使用了 await，因此实现了按顺序输出。

### 闭包
函数和对其周围状态的引用捆绑在一起构成闭包。闭包可以让内部函数访问外部函数作用域。在 JavaScript 中，每当函数被创建，就会在函数生成时产生闭包。

**闭包的使用**

```JavaScript
  (function () {
    for (var i = 0; i < 3; i++) {
      setTimeout(function () {
        console.log(i);
      }, 0);
    }
  })();
```

上面代码在 for 循环中定义了三个定时器，分别在回调函数中打印当次循环中 i 的值，但打印的都是 3。因为它们共享一个词法作用域，在这个作用域中存在一个变量 i。而变量 i 是使用 var 声明的，由于变量提升所以具有函数作用域。在回调函数执行的时候循环已经执行完毕，变量 i 为 3。

解决这个问题的一种方法是使用闭包：

```JavaScript
  function foo(value) {
    return function() {
      console.log(value);
    }
  }
  (function () {
    for (var i = 0; i < 3; i++) {
      setTimeout(foo(i), 0);
    }
  })();
```

上面代码可以如我们期望的那样工作，所有的回调不再共享一个词法作用域，foo 函数为每一个回调创建了一个新的词法环境。value 的值对应的当次循环中 i 的值。

还有一种方法是使用 IIFE 闭包：

```JavaScript
  (function () {
    for (var i = 0; i < 3; i++) {
      (function(value) {
        setTimeout(() => console.log(value), 0);
      })(i);
    }
  })();
```

不想使用闭包的话可以使用 let 代替 var，或者使用 数组的遍历方法（forEach、map 等)：

```JavaScript
  (function () {
    [1, 2, 3].forEach(value => {
      setTimeout(() => console.log(value), 0);
    })
  })();
```

### 垃圾回收和内存泄露
垃圾回收，把不再使用的引用置空，垃圾回收机制会定期的释放没法使用的引用的内存。内存泄露就是分配了内存，但没有被使用，没有及时释放或者没法释放。以下场景可能造成内存泄露：
1. 没有显式声明的变量，会成为全局变量
2. 没有及时清除的定时器、没法清除的定时器
3. 不再使用的引用，如已经清空的 DOM 节点引用

### 数组方法
#### 修改器方法

下面这些方法会改变调用它们的对象自身的值。

- Array.prototype.pop()：删除数组的最后一个元素，并返回这个元素。
- Array.prototype.push()：在数组末尾增加一个或多个元素，并返回数组的新长度。
- Array.prototype.shift()：删除数组的第一个元素，并返回这个元素。
- Array.prototype.unshift()：在数组的开头增加一个或多个元素，并返回数组的新长度。
- Array.prototype.sort()：对数组元素进行排序，并返回这个数组。
- Array.prototype.reserve()：颠倒数组中元素的排列顺序，即原先的第一个变为最后一个，原先的最后一个变为第一个。
- Array.prototype.splice()：在任意的位置给数组添加或删除任意个元素。

#### 访问方法

下面的这些方法绝对不会改变调用它们的对象的值，只会返回一个新的数组或者返回一个其他的期望值。

- Array.prototype.concat()：返回一个由当前数组和其他若干个数组或若干个非数组值组合成的新数组。
- Array.prototype.join()：连接所有数组元素组成一个字符串。
- Array.prototype.slice()：抽取当前数组中的一段元素组成一个新数组。
- Array.prototype.toString()：返回一个由所有数组元素组合而成的字符串。
- Array.prototype.indexOf()：返回数组中第一个与指定值相等的元素的索引，如果找不到这样的元素，则返回 -1。
- Array.prototype.()：返回数组中最后一个与指定值相等的元素的索引，如果找不到这样的元素，则返回 -1。

#### 迭代方法

- Array.prototype.forEach()：为数组中的每个元素执行一次回调函数。
- Array.prototype.every()：如果数组中的每个元素都满足测试函数，则返回 true，否则返回 false。
- Array.prototype.some()：如果数组中至少有一个元素满足测试函数，则返回 true，否则返回 false。
- Array.prototype.filter()：将所有的在过滤函数中返回 true 的数组元素放进一个新数组中并返回。
- Array.prototype.map()：返回一个由回调函数的返回值组成的新数组。
- Array.prototype.reduce()：从左到右为每个数组元素执行一次回调函数，并把上次回调函数的返回值放在一个暂存器中传给下次回调函数，并返回最后一次回调函数的返回值。
- Array.prototype.reduceRight()：从右至左为每个数组元素执行一次回调函数，并把上次回调函数的返回值放在一个暂存器中传给下次回调函数，并返回最后一次回调函数的返回值。

### 数组乱序
洗牌算法：原理是遍历数组，将当前元素和随机抽取的一个元素进行交换。

```JavaScript
  (function () {
    let arr = [1,2,3,4,5,6,7,8,9,10];
    let len = arr.length;
    for (var i = len - 1; i > 0; i--) {
      let index = Math.floor(Math.random() * (i + 1));
      let temp = arr[i];
      arr[i] = arr[index];
      arr[index] = temp;
    }
    console.log(arr);
  })();
```

还有一种写法，遍历数组将随机抽取的一个元素放到一个新数组中。

```JavaScript
  (function () {
    let arr = [1,2,3,4,5,6,7,8,9,10];
    let tempArr = Array.from(arr);
    let len = arr.length;
    let retArr = arr.map((item, index) => {
      let ind = Math.floor(Math.random() * (len - index));
      return tempArr.splice(ind, 1)[0];
    })
    console.log(retArr);
  })();
```

### 数组扁平化
**Array.prototype.flat([depth])**

```JavaScript
  (function () {
    let arr = [1,[2,3],[4,[5,],6],7,8,9,10];
    console.log(arr.flat(Infinity));
  })();
```

**使用扩展运算符 ...**

```JavaScript
  (function () {
    let arr = [1,[2,3],[4,[5,],6],7,8,9,10];
    const flattened = arr => [].concat(...arr);
    console.log(flattened(arr));
  })();
```

**forEach + isArray + push + recursivity 实现多元数组扁平化**

```JavaScript
  (function () {
    let arr = [1, [2, 3], [4, [5], 6], 7, 8, 9, 10];
    let flatArr = [];
    (function flatten(arr) {
      arr.forEach(item => {
        if (Array.isArray(item)) {
           flatten(item);
        } else {
          flatArr.push(item);
        }
      })
    })(arr);
    console.log(flatArr);
  })();
```

### 事件
#### 事件监听
**事件处理属性（DOM 0）**

事件处理程序属性的功能和选项会更少，但是具有更好的跨浏览器兼容性（支持 IE8）。

```JavaScript
  element.onclick = function () {};
```

但是不支持为一个事件处理属性指定多个处理函数。

```JavaScript
  element.onclick = function foo(){};
  element.onclick = function bar(){};
  // 最终绑定的回调函数是 bar
```

**addEventListener() 和 removeEventListener()（DOM 2）**

可以给同一个监听器注册多个处理器，当元素被点击时两个函数都会执行：

```JavaScript
  element.addEventListener('click', foo);
  element.addEventListener('click', bar);
```

还可以使用 removeEventListener() 方法移除事件监听器：

```JavaScript
  // 注册多个处理器
  element.addEventListener('click', foo);
  element.addEventListener('click', bar);
  // 移除事件监听器
  element.removeEventListener('click');
```

#### 事件模型
事件冒泡和捕获是两种机制，主要描述当在一个元素上有两种相同类型的事件处理器被激活会发生什么。

**对事件冒泡和捕获的解释**

当一个事件发生在具有父元素的元素上时，现代浏览器运行两个不同的阶段 —— 捕获阶段和冒泡阶段。在捕获阶段：

- 浏览器检查元素的最外层祖先\<html\>，是否在捕获阶段注册了一个 onclick 事件处理程序，如果是，则运行它。
- 然后，它移动到\<html\>中单击元素的下一个祖先元素，并执行相同的操作，然后是单击元素再下一个祖先元素，以此类推，直到到达实际点击的元素。

在冒泡阶段，恰恰相反：

- 浏览器检查实际点击的元素是否在冒泡阶段中注册了一个 onclick 事件处理程序，如果是，则运行它。
- 然后它移动到下一个直接的祖先元素，并执行相同的操作，然后是下一个，直到它到达 \<html\> 根元素。

**使用 stopPropagation() 禁止冒泡**

在现代浏览器中，默认情况下，所有事件处理程序都在冒泡阶段进行注册。如果想在捕获阶段注册一个事件，那么可以通过使用 addEventListener() 注册事件处理程序，并将可选的第三个参数设置为 true。

标准事件对象具有可用的名为 stopPropagation() 的函数，在事件对象上调用该函数时，它只会让当前事件处理程序运行，但事件不会在冒泡链上进一步扩大，因此不会有更多的事件处理器被运行（不会向上冒泡）。

#### 事件委托
冒泡还允许我们利用事件委托——这个概念依赖这样一个事实，如果你想要在大量子元素中单击任何一个都可以运行一段代码，那么可以将事件监听器设置在其父节点上，并让子节点上发生的事件冒泡到父节点上，而不是每个子节点单独设置事件监听器。
