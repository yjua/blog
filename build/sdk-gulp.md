现在流行 `mvvm` 三大框架，各种`cli` 工具也给我们提供了很多方便。我在工作中使用的是vue 技术栈。`vue-cli` 让我不能更爽了。分分钟拉起架子，开始各种业务代码。


但是，工作中我还遇到另一种情况，就是我需要写一些 `SDK` 文件，有对内的也有对外的。每次写这个都让我蛋疼的不要不要的，如果使用 `cli` ，需要依赖，文件太大不合适。如果手工去写，好多`es6`的语法用不上，还得自己处理 `polyfill`。 索性根据需要自己搭建一套构建工具。




## 需求


开始之前，我们先分析下需要哪些功能。`SDK` 一般都是提供一些Api方法，其中绝大多数可能都是JS。所以我们可能需要以下的一些能力。


+ ES6 转义为 ES5 语法
+ 有可能需要分模块，需要处理 `import` 。
+ 压缩




## 开始


`webpack` 和 `gulp` 都是目前比较流行的构建工具。这次我们使用 gulp 来构建。


首先新建一个空的文件`sdk-cli`。然后执行以下命令，初始化一个package.json 文件。


```shell
npm init -y
```


下一步，我们全局和本地安装gulp。


```shell
npm install -g gulp
npm install gulp --save-dev
```


我们在跟目录下新建一个 ``gulpfile.js` `。


```javascript
// gulpfile.js
const gulp = require('gulp');
```

同时，我们在根目录下创建一个`app.js` 文件。

```javascript
//  app.js   测试代码
let SDK = {
    show: () =>{
        console.log('this is function')
    },
    init(options){
        let _opt = {
            name:'zhangsan',
            age: 23
        }
        options = Object.assign(_opt,options);
        console.log(options);
    }
}

window.SDK = SDK;
```





## 编译ES6

下一步，我们开始编译上面的`app.js` 文件，最后输出。


编译ES6，我们就需要用到 `gulp-babel` 这个插件了。


```shell
npm install gulp-babel --save-dev
```

同时，我们需要创建一个 `.babelrc` 的文件，这个文件是定义了一些 `babel` 的规则。

```json
// .babelrc
{
	"presets": [
		[
			"@babel/preset-env",
			{
				"useBuiltIns": "usage",  //根据代码的需要加载polyfill
				"corejs": 3  // 依赖corejs-3
			}
		]
	]
}
```

主要是把es6的语法转换为es5的语法。

下面我们往 `gulpfile.js` 中添加一些代码。

```javascript
const gulp = require('gulp');
const babel = require('gulp-babel');

function js(){
    return gulp.src('./app.js') // 输入 app.js
        .pipe(babel())		// babel 转换代码 
        .pipe(gulp.dest('./dist/'))	  	// 输出到dist文件夹下，会自动创建文件夹
}
exports.js = js; // 输出task任务为js
```

然后我们执行构建代码

```powershell
gulp js
```

然后我们会看到报错

```javascript
$ gulp js
internal/modules/cjs/loader.js:638
    throw err;
    ^

Error: Cannot find module '@babel/core'
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:636:15)
    at Function.Module._load (internal/modules/cjs/loader.js:562:25)
    at Module.require (internal/modules/cjs/loader.js:690:17)
    at require (internal/modules/cjs/helpers.js:25:18)
    at Object.<anonymous> (D:\hlmy\learning\sdk-clis\node_modules\gulp-babel\index.js:7:15)
    at Module._compile (internal/modules/cjs/loader.js:776:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:787:10)
    at Module.load (internal/modules/cjs/loader.js:653:32)
    at tryModuleLoad (internal/modules/cjs/loader.js:593:12)
    at Function.Module._load (internal/modules/cjs/loader.js:585:3)
```

这是因为虽然我们安装了 `gulp-babel` ，他只是一个工具库，他还需要用到 `@babel/core` 这个核心库。同样道理，也需要安装`@babel/preset-env`

```shell
npm install @babel/core @babel/preset-env -D
```

安装完之后，我们在试着跑下命令

```shell
gulp js
```

我们会看到这次成功了，在 `dist ` 文件夹下输出了一个 `app.js`文件。

他是长这样的，

```javascript
"use strict";

require("core-js/modules/es.object.assign");

//  app.js   测试代码
var SDK = {
  show: function show() {
    console.log('this is function');
  },
  init: function init(options) {
    var _opt = {
      name: 'zhangsan',
      age: 23
    };
    options = Object.assign(_opt, options);
    console.log(options);
  }
};
window.SDK = SDK;
```

跟我们的源文件比一下，会发现他把一些es6的语法，比如 `箭头函数` , `对象写法` 转换成了 `es5`的语法。

`babel` 转换是分为两部分，一个是转换语法，一个是转换 `api`。默认情况下直接转换语法，api部分是通过`polyfill`来实现的。

例如： `Object.assign` 就是通过引入`require("core-js/modules/es.object.assign");` 来解决。

这是因为，babel 默认是转换成`commonjs` 格式的，这是啥意思呢，就是说他最终转换成能在node 服务中使用。

如果我们想要在浏览器中再次能使用，就得再次处理。比如下面这个插件。



## browserify

```shell
npm install browserify vinyl-source-stream -D
```

我们在改一下 `gulpfile.js` 文件

```javascript
const gulp = require('gulp');
const babel = require('gulp-babel');
const browserify = require('browserify');
const source = require('vinyl-source-stream');

function js(){
    return gulp.src('./app.js') // 输入 app.js
        .pipe(babel())		// babel 转换代码 
        .pipe(gulp.dest('./dist/'))	  	// 输出到dist文件夹下，会自动创建文件夹
}

function browser(){
    var b = browserify({
        entries: "dist/app.js"
    });
    return b.bundle()
        // 将常规流转换为包含 Stream 的 vinyl 对象
        .pipe(source("bundle.js"))
        .pipe(gulp.dest("dist/"));
}

exports.js = js; // 输出task任务为js
exports.browser = browser;
```

这次我们引入了两个插件`browserify` 和 `vinyl-source-stream` 。

`browserify` 就是把 `commonjs` 格式转换成 浏览器可用的文件。功能很强大，我们这里主要使用的是他的基本功能。

`vinyl-source-stream`  这个插件是把常规的数据流转换成包含 `Stream`的  `vinyl` 对象。

### 数据流

我们都知道 `gulp` 是流处理，但是它的流和 我们通常所说的 `node` 的流不是一个东西。`gulp`的流是基于 `vinyl` , 这是一个虚拟对象格式。



我们去 vinyl 的官网看下，他的用法是这样的。

```javascript
var Vinyl = require('vinyl');

var jsFile = new Vinyl({
  cwd: '/',
  base: '/test/',
  path: '/test/file.js',
  contents: new Buffer('var x = 123')
});
```

vinyl格式除了内容外，还包含一些路径的信息。它的contents总共有三种格式，`Stream` `Buffer` 和 `null`。 

而gulp 默认使用的是 包含 `Buffer`  的 `vinyl` 对象。



### vinyl的作用

那为什么gulp 不使用普通的 node 的Stream流呢，而要自己弄一个 vinyl 虚拟格式呢？我们看一下如下代码：

```javascript
function css(){
    gulp.src('./css/**/*.css')
    	.pipe(gulp.dest('./dist'))
}
exports.css = css;
```

这段代码的意思是，把css目录下的所有css文件，复制到dist目录下。

请注意这个包含多级目录，我们知道gulp会自动创建没有的目录和文件名。而node 的stream 流呢，他只传输Stream Buffer这些，不关心路径的问题。所以就需要使用 `vinyl` 格式。

其实 `vinyl` 虚拟格式是一个对普通流的包装。



## 安装core-js

言归正传，我们接着写构建。如果你现在去运行构建命令 `gulp browser`，是会报错的。为什么呢，因为我们还需要安装`core-js` 。gulp 的 `polyfill` 都是通过这个库包含的，所以我们需要安装。

```shell
npm install core-js -D
```

然后我们在运行一下

```shell
gulp browser
```

我们打开 `dist `看一下，下面有两个文件`app.js` 和 `bundle.js`。

`bundle.js` 使我们最终所需要的文件。



## 扫尾工作

主要转换工作，已经做完。剩下我们还需要一些扫尾工作，比如 `压缩` 。

上面是两步操作，所以我们也需要合并，删除一些中间文件。

最终如下：

```javascript
// gulpfile.js

const gulp = require('gulp');
// 编译es6
const babel = require('gulp-babel');
// 编译commonjs
const browserify = require('browserify');
// 转换为stream的vinyl对象
const source = require('vinyl-source-stream');
// 转换为buffer的vinyl对象
const buffer = require('vinyl-buffer');
// 压缩
const uglify = require("gulp-uglify");
// 清除文件
const clean = require('gulp-clean');

function js(){
    return gulp.src('./app.js') // 输入 app.js
        .pipe(babel())		// babel 转换代码 
        .pipe(gulp.dest('./dist/'))	  	// 输出到dist文件夹下，会自动创建文件夹
}

function browser(){
    var b = browserify({
        entries: "dist/app.js"
    });
    return b.bundle()
        // 将常规流转换为包含 Stream 的 vinyl 对象
        .pipe(source("bundle.js"))
        // 将 vinyl 对象内容中的 Stream 转换为 Buffer
        .pipe(buffer())
        // 压缩
        .pipe(uglify())
        .pipe(gulp.dest("dist/"));
}

// 删除中间文件
async function del(){
    await gulp.src('./dist/app.js')
        .pipe(clean())
}

exports.js = js; // 输出task任务为js
exports.browser = browser;
// 顺序执行task
exports.es = gulp.series( js,browser,del)

```



差不多到现在为止，一个简单的构建就出来了。可用满足使用了。

另外，`gulp`，`babel` `browserify` 都是很灵活的。所以以上的用法只是其中的一个，大家如果有兴趣也可以尝试用其他的一些插件来完成。整体思路是这样。

如果大家对 构建工具开发SDK，有什么好的方法或者新的。可以留言一起探讨。



所有的代码在 [github](https://github.com/yjua/demo/tree/master/sdk-cli)上，有需要可以自己查看。

参考链接：

https://segmentfault.com/a/1190000003770541#articleHeader8

https://zhuanlan.zhihu.com/p/58624930

https://github.com/browserify/browserify#usage

https://gulpjs.com/docs/en/getting-started/quick-start

https://www.babeljs.cn/docs/