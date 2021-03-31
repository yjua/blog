



## 思路

首先所有元素中随机选择一个与最后一个进行交换，然后在除去最后一个的元素中选择一个与倒数第二个进行交换，一直到最后一个元素。



## 复杂度

复杂度为O(n)



## 算法

```javascript
// 从后往前 倒叙
function shuffle(arr) {
    var shuffled = arr.concat();

    for (var i = shuffled.length-1; i>= 0; i--) {
        var rand = Math.floor(Math.random() * (i + 1));
      	// 交换 方式一
        var temp = shuffled[i];
        shuffled[i] = shuffled[rand];
        shuffled[rand] = temp;
      
      	// 交换方式二 
      	//[shuffled[i],shuffled[rand]] = [shuffled[rand],shuffled[i]] 
    }

    return shuffled;
}


// 运行结果
var arr = [];
for(var i =0;i< 10 ;i++){
    var k = Math.ceil(Math.random() * 100)
    arr.push(k);
}
console.log('------start---------')
console.log(arr)
console.log(shuffle(arr));
console.log('-----end--------')


```



## 证明随机性

对 n 个数进行随机：

1. 首先我们考虑 n = 2 的情况，根据算法，显然有 1/2 的概率两个数交换，有 1/2 的概率两个数不交换，因此对 n = 2 的情况，元素出现在每个位置的概率都是 1/2，满足随机性要求。
2. 假设有 i 个数， i >= 2 时，算法随机性符合要求，即每个数出现在 i 个位置上每个位置的概率都是 1/i。
3. 对于 i + 1 个数，按照我们的算法，在第一次循环时，每个数都有 1/(i+1) 的概率被交换到最末尾，所以每个元素出现在最末一位的概率都是 1/(i+1) 。而每个数也都有 i/(i+1) 的概率不被交换到最末尾，如果不被交换，从第二次循环开始还原成 i 个数随机，根据 2. 的假设，它们出现在 i 个位置的概率是 1/i。因此每个数出现在前 i 位任意一位的概率是 `(i/(i+1)) * (1/i) = 1/(i+1)`，也是 1/(i+1)。
4. 综合 1. 2. 3. 得出，对于任意 n >= 2，经过这个算法，每个元素出现在 n 个位置任意一个位置的概率都是 1/n。



## 总结

洗牌算法是经典算法，完全随机算法。sort并不是完全随机分布