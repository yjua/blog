> 浅拷贝深拷贝，是js比较基础的一个知识点。也是面试经常被问到的一个知识点。
>
> 现在我们从新梳理下关于深拷贝浅拷贝要注意的东西，鉴于网上相关内容也不少，所以一些基础的概念性的东西就不在重复了。

 

### 浅拷贝

浅拷贝就比较简单了，就是只对第一层进行复制。但是我们要注意，浅拷贝`不是赋值`，`不是赋值`，`不是赋值`。重要的基础性的东西要强调下。

下面这些常见的方法就是浅拷贝。

**Object.assign**

```javascript
var a = {
    x: 11,
    y: 333
}
var b = {
    x: {
        z: 444
    },
}
var c = Object.assign(a,b);
 
// c.x.z === b.x.z
```

 

### 深拷贝

深拷贝就复杂了些，下面列举一下常用的几个方法，以及中间我们会遇到什么坑。

#### **JSON**

```javascript
JSON.parse(JSON.stringify(obj))
```

这个方法看着简单易用，对一些常用的类型也确实够用了，但是在一些类型上就不行了。

```javascript
var obj = {
    a: 122,
    b: 'zifu',
    c: true,
    d: null,
    e: undefined,
    f: Symbol('flag'),
    g: new Date(),
    h: /\d+/ig,
    i: function(){console.log('function')},
    j: Object.create(
        // foo 是个继承属性。
        {foo: 1}, {
            // 不可枚举
            x: { value: 'x', enumerable: false },
            // 可枚举
            y: { value: 'y', enumerable: true }
        }
    ),
    k:[1,2,3],
    l: new Set([1, 2, 3, 4, 4]),
    m:new Map([
        ['name', '张三'],
        ['title', 'Author']
    ])
}
var copy = JSON.parse(JSON.stringify(obj))
 
// copy 值为
// {
//     a: 122
//     b: "zifu"
//     c: true
//     d: null
//     g: "2019-07-02T07:57:52.213Z"
//     h: {}
//     j: {y: "y"}
//     k: [1, 2, 3]
//     l: {}
//     m: {}
// }
```

 

通过上面的例子我们可以看到有一些类型使用这个方法是无效的：

+ `undefined` 、`Symbol` 、`function`是直接忽略了。
+ 日期类型，转换为了一个字符串。
+ 正则直接转换为了一个空对象。
+ 继承属性和不可枚举对象是没有办法进行拷贝的。
+ `set` `map` 都转换为了空对象。

 

所以如果我们要使用这个方法进行深拷贝的时候一定要注意这些特殊情况，如果要操作的对象有这些特殊的的情况，是没有办法使用这个方法的。

 

#### **实现**

那我们现在来尝试着通过递归来实现一个深拷贝。

这里面要解决两个问题：

+ 循环引用
+ 多种类型

```javascript
function isObject(obj) {
  return Object.prototype.toString.call(obj) === '"[object Object]"'
}

function deepClone(target,hash = new WeakMap()){
  // 基础类型 和 function return
  const isObj = isObject(target);
  const isArr = Array.isArray(target);
  // 非object 和 array 直接返回
  if(!isObj && !isArr){
    return target;
  }
  // 如果有值 则返回，避免重复死循环，解决环的问题
  if(hash.get(target)){
    return hash.get(target)
  }

  let result = isArr ? [] : {}
  hash.set(target,result);

  // 循环复制，遇到[] 和 {} 进行递归
  for(let key in target){
    result[key] = isObject(target[key]) ? deepClone(target[key],hash) : target[key]
  }
  return result;
}
```

 

解决循环引用，上面我们使用了 `WeakMap` 进行了存储值，遇到重复的值。我们就直接使用，不需要继续深入递归，陷入死循环。 

类型只做了数组和object。



### 总结

最后我们来总结一下，深拷贝到底是在拷贝什么？

从直观上来理解，就是我们要复制一份一模一样，但是又没有一点关系的一个镜像。

我们都知道 **JS** 是基于原型链的，我们可以在原型链上一层一层的网上查找，并且我们还可以对原型链进行一些操作。正常来讲深拷贝我们也需要考虑这些。

还有一些内容我们是可以设置 **不可枚举** 的，那我们深拷贝的时候，是不是也需要考虑的呢？

除了这些，还有 **正则** **日期**  **Symbol**  **Buffer**  **TypedArray**  等等。



### 建议

+ 当然在正常的使用中，数组和普通对象就足够了。建议序列号和反序列化。

+ 遇到复杂些的，我们可以使用 **lodash**  这个库的**cloneDeep** 方法。这个库是目前来说深拷贝覆盖类型比较全的了。
+ 为了学习，可以把上面对应的每一类分别去研究。



 

### 参考链接

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign