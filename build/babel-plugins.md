[TOC]

## 核心组成插件

### `@babel-core`

核心功能，不参与转换。转换功能全部交予插件完成。

 

### @babel/cli

命令行工具，使用这个可以直接在命令行使用babel的一些命令。

 

### @babele/node

是 babel-cli 的一部分，在node中直接运行es2015的代码。

 

### @babel/preset-env

为了编译箭头函数，我们可以使用 `@babel/plugin-transform-arrow-functions` 这个插件。

为了编译class，我们可以使用 `@babel/plugin-transform-classes` 插件。

ES6 + 这么多语法，如果我们一个一个写，不仅麻烦还容易出错。所以就有了 `@babel/preset-env` 这个 `preset` 。

#### 注意事项：

+ 如果不写特殊配置，该预设包含所有的es6+的语法，但是不包含stage-x的插件。

+ es6 转义分两种，syntax和API。`@babel/preset-env`默认只转义新的 JavaScript 句法（syntax），而不转换新的 API。

  比如 Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise 等全局对象，以及一些定义在全局对象上的方法（比如 Object.assign ）都不会转码

 

#### 使用方法：

我们看下 `.babelrc` 的配置文件。

```javascript
// 也可以这样写
{
    "presets": [
        ["@babel/preset-env", {
            "targets": {
                "modules": "umd", //以特定格式输出 值有 amd umd systemjs commonjs false
                "browsers": ["last 2 versions", "safari >= 7"], //支持所有浏览器的最新两个版本（但是safari >= 7）
                "useBuiltIns": "usage", //指定了如何处理polyfill，依赖corejs所以需要安装corejs
                "corejs": 3 // 如果使用了useBuiltIns则需要使用该字段，不写默认是版本2
                //"node": "6.10"  node模式
                //node: 'current'   表示最新稳定版
            }
        }]
    ]
}
```

useBuiltIns参数，这个参数就是解决编译es6 + `api`的polyfill，它依赖`corejs`，需要安装。

具体可以查看官网关于 `@babel/preset-env` 的配置信息：https://www.babeljs.cn/docs/babel-preset-env

 

### core-js

这是一个JavaScript的标准库，包含es5、es6+的方法和提案的polyfills的实现。它几乎包含了所有的polyfill，但是几乎不等于全部。generator 它没有实现。

 

### regenerator

这是Facebook的一个库，主要是实现了generator/yeild、async/await。

 

### `@babel/polyfill`

> `@babel/polyfill` 包含了库 `regenerator runtime`  和 `core-js`。

上面我们也说了，默认情况下babel只转义语法。而api，则需要使用polyfill进行特殊处理。

这个模块模拟了一个完整的ES2015+ 的环境，主要是为了用于应用程序，它会改变一些全局的变量，原型和类的静态方法。

+ 第一种方法

我们可以这么使用，在头部引入：

```javascript
import '@babel/polyfill'
```

上面的这种引入方式会引入全部的 polyfill。如果我们只使用了其中的一部分，引入全部则会显得臃肿。

+ 第二种方法

我们不需要再每个文件中 import 引入，使用babel的 `useBuiltIns` 参数。

####  使用方法：

```javascript
// .babelrc
// 配置polyfill按需加载，以及句法转换
{
    "presets": [
        ["@babel/preset-env", {
            "useBuiltIns": "usage",
            "corejs": 3,
            "modules": "umd"
        }]
    ]
}
```

 

### `@babel/runtime`

> `@babel/runtime` 是一个包含Babel 模块运行时助手和 `regenerator-runtime` 的库。

```shell
npm install --save @babel/runtime
npm install --save-dev @babel/plugin-transform-runtime
```

为什么要用两个插件呢？当我们使用`@babel/runtime` 的时候。

源代码：

```javascript
class Circle {}
```

编译为

```javascript
function _classCallCheck(instance, Constructor) {
  //...
}
 
var Circle = function Circle() {
  _classCallCheck(this, Circle);
};
```

此时假如我们有多个文件，每个文件都用到了class，则每个文件都会编译一个`_classCallCheck` 造成重复。如果启用 `@babel/plugin-transform-runtime` 就能重复内容引用同一个了。

编译为

```javascript
var _classCallCheck = require("@babel/runtime/helpers/classCallCheck");
 
var Circle = function Circle() {
  _classCallCheck(this, Circle);
};
```

####  使用方法：

```javascript
// .babelrc
// plugin-transform-runtime插件配置polyfill按需加载，以及句法转换
{
    "plugins": [
        ["@babel/plugin-transform-runtime", {
            "corejs": 2
        }]
    ],
    "presets": ["@babel/preset-env"]
}
```

该插件的参数corejs默认为false，需要用户手动自己引入polyfill。

如果不想手动引入，则需要设置值为数字2或3。他们分别依赖如下插件：

```shell
npm install --save @babel/runtime-corejs2
npm install --save @babel/runtime-corejs3
```

 

这也就意味着插件`@babel/plugin-transform-runtime` 依赖  `@babel/runtime` 变成了 `@babel/plugin-transform-runtime` 依赖  `@babel/runtime-corejs2`

 

### @babel/polyfill 和 @babel/runtime区别

这两个都可以把ES6+的新的API进行转义。

区别主要是`@babel/polyfill`会修改全局或者内置对象（不是一定会污染，会先判断当前环境有没有该方法，如果没有才会进行重写），而`@babel/runtime` 不会。

比如：同样转义`Object.assign()`, polyfill 则会在object上定义assign方法。而runtime，会自定义一个新的方法，从而替换掉object.assign；不会污染对象。

所以如果我们开发一个对外的公共组件，建议使用runtime，这样不会影响到其他组件的使用了。

 

 

## 官方Preset

- `@babel/preset-env`
- `@babel/preset-flow`
- `@babel/preset-react`
- `@babel/preset-typescript`

 