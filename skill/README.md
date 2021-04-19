
## 获取 URL 上的参数

```javascript
/**
 *  name 参数名称
 *  url 需要获取的url
 **/
function getQueryParams(name,url){
    let url = url || window.location.href;
  	let reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
    let r = url.substr(url.indexOf('?')).substr(1).match(reg);
    return r != null ? unescape(r[2]) : '';
}
```



## 日期格式化

```javascript
function Format(date, fmt) {
  var o = {
    "M+": date.getMonth() + 1, //月份 
    "d+": date.getDate(), //日 
    "h+": date.getHours(), //小时 
    "m+": date.getMinutes(), //分 
    "s+": date.getSeconds(), //秒 
    "q+": Math.floor((date.getMonth() + 3) / 3), //季度 
    "S": date.getMilliseconds() //毫秒 
  };
  if (/(y+)/.test(fmt)) {
    fmt = fmt.replace(RegExp.$1, (date.getFullYear() + "").substr(4 - RegExp.$1.length));
  }
  for (var k in o) {
    if (new RegExp("(" + k + ")").test(fmt)) {
      fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
    }
  }
  return fmt;
}
```

```javascript
function parseTime(time, cFormat) {
    if (arguments.length === 0) {
      return null
    }
    const format = cFormat || '{y}-{m}-{d} {h}:{i}:{s}'
    let date
    if (typeof time === 'object') {
      date = time
    } else {
      if ((typeof time === 'string') && (/^[0-9]+$/.test(time))) {
        time = parseInt(time)
      }
      if ((typeof time === 'number') && (time.toString().length === 10)) {
        time = time * 1000
      }
      date = new Date(time)
    }
    const formatObj = {
      y: date.getFullYear(),
      m: date.getMonth() + 1,
      d: date.getDate(),
      h: date.getHours(),
      i: date.getMinutes(),
      s: date.getSeconds(),
      a: date.getDay()
    }
    const time_str = format.replace(/{([ymdhisa])+}/g, (result, key) => {
      const value = formatObj[key]
      // Note: getDay() returns 0 on Sunday
      if (key === 'a') { return ['日', '一', '二', '三', '四', '五', '六'][value ] }
      return value.toString().padStart(2, '0')
    })
    return time_str
}
```



## 哈希code

> 根据不同的string参数生成不同的哈希值，保证每个string返回一个唯一值。返回数字 有可能为负数

```javascript
function hashCode(s){
    return s.split("").reduce(function(a,b){a=((a<<5)-a)+b.charCodeAt(0);return a&a},0);
}
```



## JSONP实现



```javascript
// url 请求的url
// data 请求的数据
//success 请求成功调用的url
//bl 布尔值 加载成功后是否删除
function dynamicScript(url,data,success,bl){
    var dataString = '';
    //处理calback
    var jsonpcallback = "jsonpcallback" + (Math.random() + "").substring(2);
    if (typeof data == "object" && data != null) {
        if(typeof success == 'function'){
            window[jsonpcallback] = function(){
                success();
            }
            data['callback'] = jsonpcallback;
        };
        for (var p in data) {
            dataString = dataString + "&" + p + "=" + data[p];
        }
    }
    if (url.indexOf("?") > 0) {
        url = url + "&" + dataString;
    } else {
        url = url + "?" + dataString;
    }
    var script = document.createElement('script');
    script.type = "text/javascript";
    script.src = url;
    script.onload = function(){
        if(bl){
            head.removeChild(script);
        }
    }
    var head = document.getElementsByTagName('head').item(0);
    head.appendChild(script);
}
```



## 字节长度



```javascript
var getByteLen = function (val) {
  var len = 0;
  for (var i = 0; i < val.length; i++) {
    if (val[i].match(/[^x00-xff]/ig) != null) //全角
    	len += 2;
    else
    	len += 1;
  };
  return len;
}

getByteLen('汉字长度') // 8  绝大多数汉字有2个字节构成
```



## 快速递增数组

```javascript
var cards = Array(62).fill().map((_,i)=>i+1);
```



## HSL转RGB

```javascript
function hslToRgb(H, S, L) {
    var R, G, B;
    if (+S === 0) {
        R = G = B = L; // 饱和度为0 为灰色
    } else {
        var hue2Rgb = function(p, q, t) {
            if (t < 0) t += 1;
            if (t > 1) t -= 1;
            if (t < 1 / 6) return p + (q - p) * 6 * t;
            if (t < 1 / 2) return q;
            if (t < 2 / 3) return p + (q - p) * (2 / 3 - t) * 6;
            return p;
        };
        var Q = L < 0.5 ? L * (1 + S) : L + S - L * S;
        var P = 2 * L - Q;
        R = hue2Rgb(P, Q, H + 1 / 3);
        G = hue2Rgb(P, Q, H);
        B = hue2Rgb(P, Q, H - 1 / 3);
    }
    return [Math.round(R * 255), Math.round(G * 255), Math.round(B * 255)];
}
```



## RGB转HSL

```javascript
function rgbToHsl(r, g, b){
    r /= 255, g /= 255, b /= 255;
    var max = Math.max(r, g, b), min = Math.min(r, g, b);
    var h, s, l = (max + min) / 2;

    if(max == min){
        h = s = 0; // achromatic
    }else{
        var d = max - min;
        s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
        switch(max){
            case r: h = (g - b) / d + (g < b ? 6 : 0); break;
            case g: h = (b - r) / d + 2; break;
            case b: h = (r - g) / d + 4; break;
        }
        h /= 6;
    }
    return [Math.floor(h*100), Math.round(s*100)+"%", Math.round(l*100)+"%"];
}
```



## 交换变量

```javascript
//第一种
a = a^b
b = a^b
a = a^b

//第二种
a = a+b;
b = a-b;
a = a-b;
```

## 禁止复制粘贴
```javascript
// 禁止右键菜单
document.oncontextmenu = function(){return false}
// 禁止文字选择
document.onselectstart = function(){return false}
// 禁止复制
document.oncopy = function(){return false}
// 禁止剪切
document.oncut = function(){return false}
// 禁止粘贴
document.onpaste = function(){return false}

```



## 防抖

```javascript
// 一定时间范围内如果再次触发，就暂停之前的执行，从新开始计时
//  应用：搜索框输入内容，等不再输入的时候再做操作
//       窗口大小改变，等不再改变的时候在做操作
function debounce(fn,delay){
  var id;
  return function(){
    clearTimeout(id)
    var ctx = this;
    var arg = arguments;
    fn.id = setTimeout(function(){
      fn.apply(ctx,arg)
    },delay)
  }
}
```



## 节流

```javascript
//在一定的时间范围之内，不管触发几次都执行一次
// 应用场景：
// 浏览器滚动的时候，触发更多内容加载请求
// 按钮点击防止多次触发
function throttle(fn,time){
  var id = null;
  return function(){
    var ctx = this;
    var arg = arguments;
    if(id){ return ; }
    id = setTimeout(function(){
      fn.apply(ctx,arg)
      id = null
    },time)
  }
}
```



## bind实现

```javascript
if(!Function.prototype.bind){
  Function.prototype.bind = function(thisArg,...arg){
    var fn = this;
    if(typeof fn !== 'function'){
      throw new TypeError('is not a function')
    }
    return function(){
      var args = arg.concat(Array.prototype.slice.call(arguments))
      return fn.apply(thisArg,args)
    }
  }
}
```



## call 实现

```javascript
//  方法一
Function.prototype.my_call = function(thisArg){
  thisArg = thisArg || window
  thisArg.fn = this;
  let arg = [...arguments].slice(1)
  let res = thisArg.fn(...arg);
  delete thisArg.fn
  return res;
}

//  方法二
Function.prototype.my_call = function(thisArg,...arg){
  thisArg = thisArg || window
  thisArg.fn = this;
  let res = thisArg.fn(...arg);
  delete thisArg.fn
  return res;
}
```



## apply 实现

```javascript
Function.prototype.my_apply = function(thisArg, arg){
  thisArg = thisArg || window
  thisArg.fn = this;
  let res = thisArg.fn(arg);
  delete thisArg.fn
  return res;
}
```



## 多维数组展开

```javascript
// 方法一
arr.flat(Infinity)

//方法二
function my_flat(arr){
  return arr.reduce( (pre,curr) => {
    if(Array.isArray(curr)) {
      return pre.concat(...my_flat(curr))
    }
    return pre.concat(curr)
  }, [])
}
```



## instanceof

```javascript
function instance_of(l,r){
  let obj = r.prototype
  l = l.__proto__
  while(true){
    if(l === obj){
      return true
    }else if(l === null){
      return false
    }
    l = l.__proto__
  }
}
```





## 监听资源加载错误

```javascript
// 在捕获阶段监听
window.addEventListener('error', event => { 
  // 过滤js error
  let target = event.target || event.srcElement;
  let isElementTarget = target instanceof HTMLScriptElement || target instanceof HTMLLinkElement || target instanceof HTMLImageElement;
  console.log(event)
  if (!isElementTarget) return false;
  // 上报资源地址
  let url = target.src || target.href;
  console.log(url);
}, true);
```



## Event Bus

```javascript
class Events {
  constructor() {
    this.events = new Map();
  }

  addEvent(key, fn, isOnce, ...args) {
    const value = this.events.get(key) ? this.events.get(key) : this.events.set(key, new Map()).get(key)
    value.set(fn, (...args1) => {
        fn(...args, ...args1)
        isOnce && this.off(key, fn)
    })
  }

  on(key, fn, ...args) {
    if (!fn) {
      console.error(`没有传入回调函数`);
      return
    }
    this.addEvent(key, fn, false, ...args)
  }

  fire(key, ...args) {
    if (!this.events.get(key)) {
      console.warn(`没有 ${key} 事件`);
      return;
    }
    for (let [, cb] of this.events.get(key).entries()) {
      cb(...args);
    }
  }

  off(key, fn) {
    if (this.events.get(key)) {
      this.events.get(key).delete(fn);
    }
  }

  once(key, fn, ...args) {
    this.addEvent(key, fn, true, ...args)
  }
}

```



## add 精度问题

```javascript
//  变成整数加减  再除数
export const addNum = (num1: number, num2: number) => {
  let sq1;
  let sq2;
  let m;
  try {
    sq1 = num1.toString().split('.')[1].length;
  } catch (e) {
    sq1 = 0;
  }
  try {
    sq2 = num2.toString().split('.')[1].length;
  } catch (e) {
    sq2 = 0;
  }
  m = Math.pow(10, Math.max(sq1, sq2));
  return (Math.round(num1 * m) + Math.round(num2 * m)) / m;
};

```



## 深拷贝

```javascript

// 利用 WeakMap 解决循环引用
let map = new WeakMap()
function deepClone(obj) {
  if (obj instanceof Object) {
    if (map.has(obj)) {
      return map.get(obj)
    }
    let newObj
    if (obj instanceof Array) {
      newObj = []     
    } else if (obj instanceof Function) {
      newObj = function() {
        return obj.apply(this, arguments)
      }
    } else if (obj instanceof RegExp) {
      // 拼接正则
      newobj = new RegExp(obj.source, obj.flags)
    } else if (obj instanceof Date) {
      newobj = new Date(obj)
    } else {
      newObj = {}
    }
    // 克隆一份对象出来
    let desc = Object.getOwnPropertyDescriptors(obj)
    let clone = Object.create(Object.getPrototypeOf(obj), desc)
    map.set(obj, clone)
    for (let key in obj) {
      if (obj.hasOwnProperty(key)) {
        newObj[key] = deepClone(obj[key])
      }
    }
    return newObj
  }
  return obj
}

```



## 数字千分位

```javascript
function numFormat(num){
  var res=num.toString().replace(/\d+/, function(n){ // 先提取整数部分
       return n.replace(/(\d)(?=(\d{3})+$)/g,function($1){
          return $1+",";
        });
  })
  return res;
}

var a=1234567894532;
var b=673439.4542;
console.log(numFormat(a)); // "1,234,567,894,532"
console.log(numFormat(b)); // "673,439.4542"


function numFormat(num){
    num=num.toString().split(".");  // 分隔小数点
    var arr=num[0].split("").reverse();  // 转换成字符数组并且倒序排列
    var res=[];
    for(var i=0,len=arr.length;i<len;i++){
      if(i%3===0&&i!==0){
         res.push(",");   // 添加分隔符
      }
      res.push(arr[i]);
    }
    res.reverse(); // 再次倒序成为正确的顺序
    if(num[1]){  // 如果有小数的话添加小数部分
      res=res.join("").concat("."+num[1]);
    }else{
      res=res.join("");
    }
    return res;
}

```

