利用 `ESLint`  `husky` `lint-staged`  `prettier` 几个组件，保证多人开发代码一致

## 基本原理

+ git add 到暂存区
+ 执行git commit
+ husky注册 git pre-commit钩子函数，执行lint-staged
+ lint-staged 取得所有被提交的文件依次执行写好的任务  eslint 和 prettier
+ 如果有错误，停止任务，打印错误信息。修复后执行commit
+ 成功commit，可push到远程



## 命令配置

在配置使用 package.json

```json
{
  "husky":{
    "hooks":{
      "pre-comit": "lint-staged"
    }
  },
  "lint-staged":{
    "**/*": [
      "eslint --fix --ext .js",
      "prettier --write --ignore-unknown",
      "git add"
    ]
  }
}
```

### eslint-config-prettier

  ```shell
  # 如果和eslint报错一样的地方，则可能会报两遍错误。这个插件是解决这个问题的
  # 等于是prettier规则覆盖  eslint:recommended 部分规则
  npm i -D eslint-config-prettier
  ```



在 .eslintrc.js 中的使用

```json
{
    "extends": ["eslint:recommended", "prettier"]
}
```



### eslint-plugin-prettier

```shell
  
  #这个插件会调用prettier对代码进行检查，原理是先格式化，在对比，不一样的地方标出来
  #在 rules中添加 'prettier/prettier':'error' 被标记的会抛出错误
  npm i -D eslint-plugin-prettier
```

作用：把prettier的能力集成到 eslint 中，按照 prettier 规则检查代码规范进行修复

在 .eslintrc.js 中的使用

```shell
{
    "rules":{
        "prettier/prettier":"error" // 不符合 prettier 规则的代码，要进行错误提示（红线）
    },
    "plugins": ["prettier"]
}
```

或者

```shell
{
    "extends": ["eslint:recommended","plugin:prettier/recommended"]
}
```



## 最终的配置

`.eslintrc.js` 文件配置

```js
module.exports = {
  "extends": ["prettier", "plugin:prettier/recommended"],
  "parser": "babel-eslint",
  "rules": {
    "prettier/prettier": "error",
    "strict": "off",
    "no-console": "off",
    "import/no-dynamic-require": "off",
    "global-require": "off",
    "require-yield": "off",
  },
  "plugins": ["prettier"],
  "globals": {
    "React": "readable"
  }
};
```

`.prettierrc.js` 文件

```js
module.exports = {
  "printWidth": 80,
  "semi": true,
  "singleQuote": true,
  "trailingComma": 'none',
  "bracketSpacing": true,
  "jsxBracketSameLine": false,
  "arrowParens": 'avoid',
  "requirePragma": false,
  "proseWrap": 'preserve'
};
```









