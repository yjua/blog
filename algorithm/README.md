一个算法的优劣程度，就靠两个指标 `时间复杂度` `空间复杂度` 。 一般情况下，复杂度我们习惯用 O 来表示，而且复杂度是一个大致情况，并不是一个精确的数值。



## 分析规则

首先，我们默认每行代码的运行时间是一个固定常量的时间单位，是一个常数。那么

分析方法，我们大致通过以下几点来获取

+ 循环部分，一般循环了n次，就为O(n)
+ 加法法则：如果多个循环部分并列，则总的复杂度可以通过相加获得。
+ 乘法法则：如果有嵌套循环，则需要复杂度相乘获得。

下面我们具体代码具体分析。



## 时间复杂度

### O(n)

时间复杂度，就是说运行一段代码，所消耗的大致时间。我们看下面一段代码。

```javascript
function cal(n){		
    let sum = 0;		//1
    let i = 0;			//2
    for(;i<n;i++){		//3
        sum += 1;		//4
    }					//5
    return sum;			//6
}						
```

代码在cpu中，是按代码逐行操作的。每行的执行时间是不一样的，但是由于我们是粗略计算，可以认为是一样的。我们暂定为 `line_time`

上面总共有 6 行代码。

+ 第1、2、5、6都是个需要1个 `line_time` 

+ 第3、4行代码都运行了 n 遍，各需要 n 个`line_time` 。

  所以总共时间是 （2n + 4）* line_time。
  
  

当n无限大的时候，常数对公式的影响几乎可以忽略不计，所以复杂度一般去掉常数。时间复杂度 T(n) = O(n)。



### O($n^2$)

```javascript
function cal(n){
    let sum = 0;				// 1
    let i = 0;					// 2
    let j = 0;					// 3
    for(;i<n;i++){				// 4
        j = 0;					// 5
        for(;j<n;j++){			// 6
            sum += i * j;		// 7
        }						// 8
    }							// 9
}
```

再来分析下上面的代码，函数内共有9行代码。

+ 第1、2、3各执行一次，共耗时 3 * `line_time`
+  第4、5行各执行了 n 次。共 2n * `line_time`
+  第6、7行各执行 n * n 次，共2 * n * n * `line_time`
+  第8行执行了 n *  `line_time`
+  第9行执行了 1次  `line_time`

所以总共的时间是： T(n) = 2  *  $n^2$ * `line_time`  + 3n * `line_time` +4 * `line_time` 。

简写为：2 * $n^2$  + 3n + 4

这是一个数学多项式，按最高次幂去估算，同时去掉常数。

所以时间复杂度为 T(n) = O($n^2$)



## 分析方法

通过以上我们知道，当我们在计算时间复杂度的时候，主要是分析代码的变化趋势，记录一个最大的量级就可以 了。所以我们一般只需要关注循环次数最多的那段代码就可以了。

除此之外呢，还有一些其他的特性

### 最大值法则

这个法则是说，如果有多段则取最大量级的代码。比如如下代码：

```javascript
function(){
	let sum1 = 0;
    for(let i = 0; i< 100; i++){
        sum1 += 1;
    }
    
    let sum2 = 0;
    for(let j = 0; j<n;j++){
        sum2 += 1;
    }
    
    let sum3 = 0;
    for(let k = 0; k< n; k++){
        for(let m = 0; m < n; m++){
            sum3 += k * m;
        }
    }
    
    return sum1+sum2+sum3;
}
```



这段代码大致分三部分，sum1 和 sum2 和 sum3。我们很容易分析出来各部分如下。

sum1 是循环100次，相对于可能无限的n来说，只要是一个确定的值。我们都认为是常量。常量都是O(1)。

sum2则是O(n)。

sum3是O(![$n^2$](http://latex.codecogs.com/gif.latex?n%5E%7B2%7D))。

综合三段代码复杂度，我们取最大的量级，即O(![$n^2$](http://latex.codecogs.com/gif.latex?n%5E%7B2%7D))。



### 嵌套法则

如果有两个进行嵌套，一般我们做乘法处理。

也就是T1(n) = O(f(n))  ，T2(n) =O(g(n))。则T(n) = T1(n) * T2(n) = O(f(n) * g(n))。

其中 f(n) g(n) 分别代表数学表达式。

```javascript
function s(n){
    let sum = 0;
    for(let i = 0; i < n; i++){
        sum += i;
    }
    return sum;
}
function k(n){
    let sum = 0;
    for(let i = 0; i< n ; i++){
        sum += s(n);
    }
    return sum
}
```

根据嵌套法则，T(n) = O(![$n^2$](http://latex.codecogs.com/gif.latex?n%5E%7B2%7D)) 。



## 常见时间复杂度

下面我们来看一下常见的复杂度有哪些。以下是时间复杂度是递增的。

+ O(1) 常量
+ O(logn) 对数
+ O(n) 线性
+ O(nlogn) 线性对数
+ O(![$n^2$](http://latex.codecogs.com/gif.latex?n%5E%7B2%7D)) O(![$n^3$](http://latex.codecogs.com/gif.latex?n%5E%7B3%7D)) O(![$n^k$](http://latex.codecogs.com/gif.latex?n%5E%7Bk%7D))   平方、立方、k方
+ O(![$2^n$](http://latex.codecogs.com/gif.latex?2%5E%7Bn%7D)) 指数
+ O(n!) 阶乘



### O(1)

这个表示常量，一般是指没有循环的内容。

### O(logn) 

先看一下这个是怎么来的。

```javascript
let i = 1;
while(i < n){
    i = i * 2;
}
```

这段代码的复杂度关键是在循环了多少次。初始值为1，最大不能超过n。则每次值罗列如下。

![$2^0$](http://latex.codecogs.com/gif.latex?2%5E%7B0%7D)  、![$2^1$](http://latex.codecogs.com/gif.latex?2%5E%7B1%7D) 、 ![$2^2$](http://latex.codecogs.com/gif.latex?2%5E%7B2%7D) 、 ![$2^3$](http://latex.codecogs.com/gif.latex?2%5E%7B3%7D)  。。。![$2^x$](http://latex.codecogs.com/gif.latex?2%5E%7Bx%7D)  <= n;     

x的值就是我们想要知道的循环次数。为了方便计算，我们用`=` 来代替 `<=` 。

即 ![$2^x$](http://latex.codecogs.com/gif.latex?2%5E%7Bx%7D) = n ; x = ![$log_2n$](http://latex.codecogs.com/gif.latex?log_%7B2%7Dn) 。所以时间复杂度为 O(![$log_2n$](http://latex.codecogs.com/gif.latex?log_%7B2%7Dn))。



一般情况下我们是不用关心对数的底数的，为什么呢？ 我们来看一下，上面的代码如果变成这样

```javascript
let i = 1;
while(i < n){
    i = i * 3;
}
```

则复杂度为 ![$log_3n$](http://latex.codecogs.com/gif.latex?log_%7B3%7Dn) 。而对数是可以相互转换底数的。 ![$log_3n$](http://latex.codecogs.com/gif.latex?log_%7B3%7Dn) = ![$log_32$](http://latex.codecogs.com/gif.latex?log_%7B3%7D2) * ![$log_2n$](http://latex.codecogs.com/gif.latex?log_%7B2%7Dn) 。而![$log_32$](http://latex.codecogs.com/gif.latex?log_%7B3%7D2) 是常量，我们可以忽略。所以我们一般忽略底数，统一记为 $logn$。



所以我们平常分析时间复杂度，按照代码的执行情况，一般都可以分析出来。



## 空间复杂度

空间复杂度和时间复杂度分析手法有点类似，但是空间主要是针对的内存，所以就看代码有没有开销内存。

```javascript
function a(n){
    let arr = [];
    for(let i = 0; i< n; i++){
        arr[i] = i;
    }
}
```

如上代码，刚开始声明的数组是空。没有开辟空间。循环了n次，每次添加一个空间。所以为O(n)。



## 常见空间复杂度

空间复杂度常用的有

+ O(1)
+ O(n)
+ O(![$n^2$](http://latex.codecogs.com/gif.latex?n%5E%7B2%7D))

空间复杂度对数级别的 O($logn$)  O($nlogn$) 几乎用不到，可以不用考虑。 

整体来讲，空间复杂度比时间复杂度简单。

![img](https://static001.geekbang.org/resource/image/49/04/497a3f120b7debee07dc0d03984faf04.jpg)
















































































