## 非父子组件通讯

`vue` 常用的组件通信方式有以下几种。

+  Prop，官方推荐的方法，一般是父组件像子组件进行数据传递。
+ Vuex，官方维护的状态管理库。
+ 事件总线
+ store模式（推荐）。

 

一般情况下，我们使用 `prop` 就可以了，但是当我们数据来源比较复杂、非父子结构，或者多个组件共享一些数据的时候。`prop` 可能就不够用了。这个时候就可以使用 `Vuex` 库了。

`Vuex` 功能特别全，特别适合大型项目。

假如我们的开发项目，没有那么复杂。但是又需要多出共享维护一些数据，这个时候我们就可以使用 `store` 模式或者事件总线模式。

 

## 事件总线

### 原理

我们通过 `new` 一个 `Vue` 对象实例，专门代理数据。然后通过注册和监听事件，实现data在多个组件中进行使用。

 

### 代码

```javascript
// bus.js
 
import Vue from 'vue'
let bus = new Vue();
// 我们可以把这个对象挂载在原型对象上 方便使用
Vue.prototype.$bus = bus;
```

这个`bus.js` 需要我们在入口文件 `app.js` 中引入。

我们可以利用 `Vue` 提供的 `$on` 和 `$emit` 方法，进行事件的分发和监听。

 

我们在分发的组件中这么使用

```javascript
//  a.vue
// ...
methods:{
    say:function(){
        // 分发事件  eventName 自定义 只要分发和监听能一一对应就行了
        // args 传递的参数
        this.$bus.$emit('eventName',[...args])
 
        // todo...
    }
}
```

 

我们在需要监听的组件中这么用

```javascript
// b.vue
// ...
created(){
    this.$bus.$on('eventName',args => {
        // todo ...
    })
},
// 在销毁组件之前 销毁监听
beforeDestroy(){
    this.$bus.$off('eventName')
}
```

 

## store模式

### 原理

我们利用 `Vue`提供的 `observable` 方法，让数据对象可响应。然后利用这个进行处理。

 

### 代码

我们创建一个 `store.js`

```javascript
import Vue from 'vue'
export let store = Vue.observable({
    count: 1
})
```



然后我们需要的组件中使用

``` javascript
// c.vue
// 在需要的组件中引入
import {store} from 'path/store.js'
 
export default{
    ...
     computed:{
        // 通过计算属性 直接使用
        count(){
            return store.count
        }
    },
    methods:{
        add(){
            // 通过add方法直接修改store.count 值，计算属性的count会直接跟着变化
            store.count += 1
        }
    }
}
```

 

`observable` 需要 vue 版本`2.6.0 ` 及以上。

而且推荐用这种模式，使用起来相对来说方便一些。