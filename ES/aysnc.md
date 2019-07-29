## 异步编程

上一篇[文章](https://juejin.im/post/5cf3c2915188255b0148df98)讲了 `Promise`， `Promise` 让我们更友好的处理异步请求，尤其是在多个回调相互依赖等待的时候。但是它不是异步的最佳解决方案，所以从ES2017开始官方引入了 `async` 方法。下面我们来看一下。

### 定义

先来看一下MDN上对他的定义。

> 声明一个异步函数，返回的是一个Promise对象。而且在 async 函数中可以使用 await 表达式。



### async 是什么

不得不说标准委员会的每个定义都是有其考虑的，名字一般都言简意赅，直明其意。从字面意思我们能知道它是异步的意思。



那他到底是怎么异步的呢。我们先看下他的返回值。

官方定义async 返回一个 Promise 对象。如果在函数中直接return 的是Promise 则直接返回。如果 return 出来的不是Promise 则对其包装成Promise。



```javascript
async function a1(){
  return 'hello world'
}

let result = a1()

```

上面的demo 返回值 `result` 就是一个Promise 对象。他和其他通过 `Promise` 显示声明的对象是一样的，也具备三种状态，也有 `then` 等方法，也可以链式调用。

async 正是通过其返回Promise对象保证异步操作。



### await 等待

标准中还说了，async中可以使用 await ，后面跟一个表达式。这个表达式可以是一个Promise对象或者任何其他的表达式。

那这个await的作用是什么呢？

await 会暂停当前 async 方法的执行，等待后面的Promise处理完成。这里我们要注意，await 暂停的是async 方法内的语句执行，但是不会阻塞async 方法之后的内容。

```javascript
async function a1(){
    console.log('a1 start')
    await new Promise((resolve,reject) => {
        setTimeout( () => {
            resolve()
            console.log('resolve')
        },1000)
    })
    console.log('a1 end ')
}

function a2(){
    console.log('this is from a2')
}

a1();
a2();

// 正确输出为： 
// a1 start
// this is from a2
// resolve
// a1 end
```



我们来看一下上面的demo。首先 await 确实是暂停了执行，等待 promise有了结果在执行的后面的代码，其次是，a1方法内部awati暂停的时候，不会阻塞后面a2 方法的执行。

这是为什么呢？我们都知道JS是单线程语言，异步是它的灵魂，即使 async 方法也不会改变这个事实，它依然是单线程，依然不会阻塞。

但是在 async 函数的内部，我们使用await 看起来就跟同步执行是一样的，其实这只是一个障眼法，他只是一个语法糖。



上面await 后面的表达式是一个Promise对象，那么如果是普通的表达式呢？

我们来看下：

```javascript
async function a1(){
    console.log('a1 start')
    await a3();
    console.log('a1 end ')
}

function a2(){
    console.log('this is from a2')
}

function a3(){
    console.log('a3')
}
a1();
a2();

// a1 start
// a3
// this is from a2
// a1 end
```



这次 await 后面接了一个普通的函数，我们看到await 后面的方法立即执行了，但是a1 方法还是暂停执行。也就是说无论后面跟什么表达式，await都会暂停执行async 方法。



### 使用姿势

我们差不多大概了解了async，具体的细节你可以去看官方文档或者其他文章，已经有很多了就不罗列了。我们现在看一下他的使用场景。



假设有这么一种场景 c依赖b，b依赖a。我们来比较下promise和 async的使用区别。

```javascript
// Promise

function a(res){
    return new Promise((resolve,reject) => {
        setTimeout( () => {
            resolve(res + 100)
        },1000)
    })
}

function b(res){
    return new Promise((resolve,reject) => {
        setTimeout( () => {
            resolve(res+ 100)
        },1000)
    })
}

function c(res){
    return new Promise((resolve,reject) => {
        setTimeout( () => {
            resolve(res + 100)
        },1000)
    })
}

a(300).then(res => {
        return b(res)
    })
    .then(res => {
        return c(res)
    })
	.then(res => {
      console.log(res)
    })

// 600
```



下面我们换成async 方法。

```javascript
// async

function a(res){
    return new Promise((resolve,reject) => {
        setTimeout( () => {
            resolve(res + 100)
        },1000)
    })
}

function b(res){
    return new Promise((resolve,reject) => {
        setTimeout( () => {
            resolve(res+ 100)
        },1000)
    })
}

function c(res){
    return new Promise((resolve,reject) => {
        setTimeout( () => {
            resolve(res + 100)
        },1000)
    })
}


async function getResult(){
    var res = 300;
    res = await a(res);
    res = await b(res);
    res = await c(res);
    console.log(res);
}

getResult()

```



是不是看着更直观，更清晰了些。



另外还有个关键点要注意：

await 后面如果是异步的话，一定要是promise对象。

有两种方式，一种是上面那样自己声明，一种是跟一个 async 方法。



试想一种情景，await后面跟一个异步请求函数。如果这个函数最后返回的不是promise对象，那么他就是普通表达式。上面讲了，他会立即执行，await 表达式是拿不到你想要的异步请求的结果的。



### 容易误区

有一些常见的用法我们要注意下，以下为伪代码

```javascript
async function(){
  let a = await getDate1();
  let b = await getDate2();
}
```

这一段代码，语法是没问题。但是这个一般情况是b对a有依赖关系，如果没有关系应该这样写。

```javascript
async function(){
  let a = getDate1();
  let b = getDate2();
  await a;
  await b;
}

// 这样a，b不存在等待关系
```



同样道理，假设以下情景，b依赖a, d依赖c。你可能会这样写

```javascript
// 伪代码

let res1 = a();
let res2 = c();

await res1;
b();
await res2;
d();
```

这有个啥问题呢，就是d等待的其实是a和c耗时最长的那个。我们可以换一种方式，把他俩放在两个函数里面。

```javascript
// 伪代码
async function ab(){
  await a();
  b()
}

async function cd(){
  await c();
  d()
}

Promise.all([ab(),cd()])
```



### 错误处理

有时候我们可能会遇到await 等待的promise返回的是一个reject状态。那我们应该怎么处理。

```javascript

function a(){
    return new Promise((resolve,reject) => {
        reject('this is error')
    })
}

//第一种  trycatch 捕获
async function getResult(){
    try {
        await a()
    } catch (error) {
        console.log(error)
    }
}

getResult()

//第二种 通过catch 捕获
async function getResult(){
    await a().catch(error =>{
        console.log(error);
    })
}

getResult()

// 第三种 通过catch捕获
async function getResult(){
    await a();
}

getResult().catch(error => {
    console.log(error)
})

```



好了，差不多async 大部分知识点都理清了。主要是几个关键点要注意到。希望以上对大家有一点小小的帮助，如果有不足和错误之处，望大家多提意见。



端午假期马上就过去了，明天又可以开心的搬砖了。



参考链接：

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function