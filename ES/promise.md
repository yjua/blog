我们都知道，ES是单线程语言。所以异步编程对它来说，尤其重要。也可以说是他的核心功能。

我们常见的异步编程有很多，比如 `回调函数`、`事件监听` `发布订阅` `Promise`等。

在早期的时候，我们使用的主要方式是回调函数，但是当我们有很多回调函数需要依赖的时候，一层套一层，就会形成`回调地狱`。

回调地狱既不利于阅读也不利于维护，所以前辈先贤们为了解决这个问题，做了很多努力。其中Promise 就是其中的一种方案。



## Promise

`Promise` 是一个构造函数，返回一个 `promise` 对象。该对象有三种状态，而且状态不可逆。还有`then` `catch` `finally` 等方法。网上有大量的基础方法定义，我们就不在罗列了。




## 使用

### then的链式调用

我们知道，Promise支持链式调用，那假如中间状态变成rejected了，会怎么样呢。

```javascript
let p1 = new Promise((resolve,reject) => {
    setTimeout( () => resolve(200) , 1000)
})

p1.then(() => {
        console.log(11)
    })
    .then(() => {
        throw Error(22)
    })
    .then( () => {
        console.log(33)
    })
    .catch(err => {
        console.log('this is error',err)
    })
// 11
// this is error 22
```

当 `then` 的回调函数返回一个错误的时候，返回的是一个rejected`状态的promise。之后的链式调用被终止了，被最后的catch捕获到。



我们知道`then` 其实是可以接受两个回调函数的，我们换一个方式猜下执行顺序是什么呢？

```javascript
let p1 = new Promise((resolve,reject) => {
    setTimeout( () => resolve(200) , 1000)
})

p1.then(() => {
        console.log(11)
    })
    .then(() => {
        throw Error(22)
    })
    .then( () => {
        console.log(33)
    },fail => {
        console.log('this is from 3rd fn',fail)
    })
    .catch(err => {
        console.log('this is error',err)
    })
    
// 输出结果为：
//11
// this is from 3rd fn

```

有没有想到这个结果。

从现象看：是因为前面已经处理了rejected 所以后面catch不会在处理了。



```javascript
let p1 = new Promise((resolve,reject) => {
    setTimeout( () => resolve(200) , 1000)
})

p1.then(() => {
        console.log(11)
    })
    .then(() => {
        throw Error(22)
    })
    .then( () => {
        console.log(33)
    },fail => {
        console.log('this is from 3rd fn', fail)
    })
    .then( () => {
        console.log(44)
    })
    .catch(err => {
        console.log('this is error',err)
    })
    .then( () => {
        console.log(55)
    })
    
   
// 输出为：
// 11
// this is from 3rd fn 22
// 44
// 55
```



怎么样，有没有想到，处理错误之后，还能继续执行，哪怕在catch后面也能执行。

透过现象看本质，下面我们对上面几种情况用一个统一的解释来理解下。

+ 首先我们要知道 `then` 方法，他接受两个回调，一个处理 `fulilled` 状态， 一个处理 `rejected`状态。每次都返回一个新的promise 对象。

+ catch的本质是`obj.then(undefined, onRejected)`。

所以上面的几种情况也就好理解了：

> then 和 catch 都会返回一个promise 对象。当中间有状态变为rejected之后，他会一直往下寻找，找到最近的rejected状态处理函数，之后根据处理函数的返回值继续返回不同状态的promise对象。一直执行到链式调用的最后。
>
> 
>
> 所以我们在使用的时候要注意了，避免不必要的麻烦。一般用catch 来处理，并且放到最后。这样能统一处理。



### 几个常见题型

```javascript
let p1 = new Promise((resolve, reject) => {
    console.log(11)
    resolve()
    console.log(22)
  })
  p1.then(() => {
    console.log(33)
  })
  console.log(44)

// 结果为：  11 、22 、44、33
```

解释：Promise构造函数会立即执行，所以先是11、resolve、22，由于then是异步的，所以代码紧接着是44、最后才是33



```javascript
Promise.resolve(11)
  .then(22)
  .then(Promise.resolve(33))
  .then(console.log)

// 输出 11
```

解释：then 参数不是function的时候都会被忽略。


## 使用场景

下面我们来看一下，它的一些常用场景：

### 1、异步b依赖异步a

```javascript
// 异步请求b 依赖异步请求a
let p1 = new Promise(function(resolve,reject){
  $.post(urla,data,function(res){
    resolve(res);
  })
})

p1.then(function(res){
  $.post(urlb,data,function(){
    // 处理请求返回后的数据
  })
})
```



### 2、依赖异步a和异步b

我们有时候经常会遇到这样的一种场景，我们需要发起两个互不依赖异步请求，然后等待两个请求都返回之后再处理。

```javascript
let p1 = new Promise((resolve,reject) =>{
    setTimeout( () => resolve(100), 200)
})

let p2 = new Promise((resolve,reject) =>{
    setTimeout( () => reject(200), 300)
})

Promise.all([p1,p2])
    .then(values =>{
        console.log(values);		// [100,200]
    },fail => {
        console.log(fail)
    })
```

`all`方法返回的是一个`promise`对象，所以他也有 `then`	`catch` 等方法。



希望上面一点点内容，能够帮助大家加深理解。如有错误，不吝指正。



参考链接：

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise