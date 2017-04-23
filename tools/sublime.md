# blog

个人博客

### sublimeText3 一些常用的插件和配置信息
```js
{
	// 设置字体大小
	"font_size": 18,

	"ignored_packages":
	[
		"Vintage"
	],
	// 一行文字过多，会出现横向滚条查看不方便。
	"word_wrap": true,

	// 设置喜欢的主题
	"theme":"Seti_orig.sublime-theme",

    
}
```
```js
{
	// 设置快捷键，删除整行

	{ "keys": ["command+d"], "command": "run_macro_file", "args": {"file": "res://Packages/Default/Delete Line.sublime-macro"} }
}

```
### 快捷键使用

* ctrl + shift + p
	* 打开命令面板
* ctrl + p 
	* 输入文件名，查找想打开的文件
* ctrl + r 
	* 在文件中根据方法名查找方法，
* ctrl + g 
	* 输入行数跳转到对应的行
* ctrl + p 
	* 定位标识符
* ctrl + d 
	* 多行选中同时编辑

### 主题
`Seti_ui`个人比较喜欢这个主题，它有两种颜色风格，习惯使用Seti_orig
```javascript
Preferences -> Settings - User
```

### 插件
- HTML-CSS-JS Prettify
	
	代码美化功能，通过右键使用 

- Side​Bar​Enhancements

	sidebar增强，侧边栏右键功能增强

- DocBlockr

	文档注释，函数注释，变量注释

- Autoprefixer

	自动添加前缀功能,使用方法:找到一个 `.css` 文件，然后 `comment+shift+p` 输入命令 `Autoprefixer css`

- CSScomb

	css格式化，使用:快捷键 `ctrl+shift+c` or 打开 “Tools” 菜单，选择 “Run CSScomb”.

- Vue Syntax Highlight

	Vue官方高亮插件，写vue必备

- Minify

	JS和css压缩软件，配置如下：

	* 首先安装`Minify`插件
	* 安装 nodejs
	* 安装命令行 `npm install -g clean-css-cli uglifycss js-beautify html-minifier uglify-js minjson svgo`

	使用:右键minify

- Markdown Preview

	markdown 文件预览

	使用：`cmd + shift + p`打开命令行，输入 ` Markdown Preview` 显示相关命令
