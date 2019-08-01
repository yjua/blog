由于业务需要，之前用 gulp 定制了一个[打包工具](./sdk-gulp.md)。后来发现 Rollup 更适合去打包一个类似SDK这样的单独文件库，现在实现一个 rollup版本的。

[官网地址](https://rollupjs.org/guide/en/) 介绍说Rollup是一个 JavaScript 模块打包器。可以把 ES6+的语法转换成ES5。它更适合打包成一个库。如果想打包成一个程序，比如需要分割文件，动态加载。更推荐用webpack来完成。



## 功能

rollup 包含我们平常所需要的大部分功能、

+ `tree-shaking` 就是通过静态分析代码的 import ，只打包实际使用的代码。

```javascript
// es6 import 引入ajax函数
import {ajax} from 'utils'
ajax()
```

比如以上打包，不会把整个utils文件打包进去，只会打包使用到的 ajax 相关功能的函数。如果使用node 的require引入，就无法进行静态分析。所以也建议尽量的使用ES6的语法。

+ 多种格式

rollup 可以根据需要打包成多种格式 `umd` `commonjs` 等。



## 开始

首先要全局安装 rollup。

```shell
npm i rollup -g
```

在项目下新建一个文件 rollup.config.js

```javascript
 
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';
import babel from 'rollup-plugin-babel';
import { uglify } from "rollup-plugin-uglify";
 
 
export default {
  input: './src/app.js',
  output: {
    file: './dist/index.js',
    format: 'iife'
  },
  plugins:[
    // 如果依赖的是 node_modules 里面的库文件，则进行引入
    resolve(),
    // 把commonjs的写法 require 转换为 import 写法
    commonjs(),
    // babel进行转义
    babel({
        exclude: 'node_modules/**' // 只编译我们的源代码
      }),
      uglify()
  ]
};
```

使用babel 需要 使用babel的规则，新建文件 .babelrc

```
// .babelrc
{
    "presets": [
        [
            "@babel/preset-env",
            {
                "useBuiltIns": "usage",
                "corejs": 3
            }
        ]
    ]
}
```

其中，由于使用了polyfill，所以需要安装一下内容。

```shell
npm i -D @babel/core @babel/preset-env rollup-plugin-babel
```

由于preset-env需要借助于core-js，所以还需要安装core-js

```powershell
npm i core-js
```



以上配置可以转义 ES6+，打包成一个完整的可以运行在浏览器的文件了。下面我们来介绍一下各行代码的作用。

+ input 入口文件
+ output 输出文件
  + format是指输出的文件类型，包括 amd、cjs、es、iife、umd
+ plugins 用到的插件



## rollup-plugins-node-resolve

该插件的意思是告诉rollup 如何查找外部模块。

```javascript
// src/main.js
import answer from 'the-answer';

export default function () {
  console.log('the answer is ' + answer);
}
```

比如这个文件，如果我们配置文件不配置插件，直接打包会警告。rollup会把import 打包成require，如果在node环境中可以执行。如果在浏览器中就会报错了。

`rollup-plugins-node-resolve` 插件就是要把这些依赖包含进来。



## rollup-plugins-commonjs

有时候我们依赖的一些库，可能是 commonjs 格式出现的。该插件就是为了把 commonjs 转换为 es6 模块，方便 `rollup` 进行处理。

请注意，`rollup-plugin-commonjs`应该用在其他插件转换你的模块*之前* - 这是为了防止其他插件的改变破坏commonjs 的检测。



## rollup-plugin-babel

这个不用说了，主要是为了把 ES6+ 语法进行转义ES5。



## 最后

通过上面一些简单的配置，我们就可以把文件转义成我们最后能在浏览器使用的文件了。

其实打包最核心的概念，就是为了处理 ES6+ 的语法，转换成 ES5的语法，能让浏览器识别。而我们做的所有配置也都是为这个服务的。