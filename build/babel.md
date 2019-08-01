[TOC]

当前文章基于版本 7.x之后。

babel的主要工作流程是，解析、转换、输出。现在的`@babel-core` 不做任何转换，只是负责解析和输出等核心功能。

+ 解析：讲代码转换成AST语法树
+ 转换：操作AST树，生成新的AST。babel的插件都是在这个过程中进行工作的
+ 生成：最后根据新的AST树，生成新的代码

 

`babel` 默认转换 `es6` 到 `es5` 的语法，是 `commonjs` 格式。也就是说最后有 **require** 这个字段的，直接在浏览器上是不能用的。如果需要在浏览器上使用，需要我们使用其他插件再次处理，比如`browserify`

 

### 核心概念
+ 插件（plugin）
  + 每个插件完成一件事，babel就是靠各种插件来完成。
  + 语法插件
    + 该插件只解析（parse）特点类型语法，不会转换。
  + 转换插件
    + 转换语法，使用该插件会自动启用语法插件。如果使用转换插件，不需要再使用语法插件。
+ 预设（preset）
  + 一堆插件的集合，方便使用不需要安装很多


```json
// .babelrc  文件配置
{
    "presets":[
        "@babel/env","stage-2"
    ],
    "plugins":[]
}
```

### 插件&预设

#### 执行顺序

+ `plugins` 在`presets`之前运行
+ `plugins` 是从前到后顺序执行。
+ `presets` 是从后到前执行。

 

#### 参数

插件和 `presets` 都支持参数。参数和插件名组成一个数组。格式如下：

```json
{
    "plugins":["pluginA",["pluginA"],["pluginA",{}]]
}
```

`presets` 的参数和上面一样的格式。

 

preset 分为以下几种：

+ 官方的，目前包括 `@babel/preset-env` `@babel/preset-flow` `@babel/preset-react` `@babel/preset-typescript`。

+ stage-x，包含各种草案等

  + stage 0  稻草人：只是一个设想，TC39成员提出即可
  + stage 1 提案：初步尝试
  + stage 2 初稿：完成初步规范
  + stage 3 候选： 完成规范和浏览器初步实现
  + stage 4 完成： 下一年度发布。

  一般低一级的包含高级的stage内容，比如stage 2 包含 3 的内容。

  stage-4 下一年就直接更新到env中，所以没有单的的stage-4使用。

 

es201X，latest

早期的时候分es2015,es2016等等。后来env出现之后，es201x都废弃了，latest是env的雏形，每年更新，它目的是包含所有es201x，现在也废弃了。

 

参考链接

https://zhuanlan.zhihu.com/p/58624930

https://zhuanlan.zhihu.com/p/43249121