[TOC]



### 什么是subtree

subtree主要是实现一个仓库作为其他仓库的子仓库，也可以设置部分代码设置为其他分支的内容。



### 命令

```shell
git subtree add   -P <prefix> <commit>
git subtree add   -P <prefix> <repository> <ref>
git subtree pull  -P <prefix> <repository> <ref>
git subtree push  -P <prefix> <repository> <ref>
git subtree merge -P <prefix> <commit>
git subtree split -P <prefix> [OPTIONS] [<commit>]
```



+ add
  + 添加另一个仓库内容到当前仓库作为子仓库，默认会把两一个仓库的提交记录全部继承过来
+ merge
  + 合并提交内容
+ pull 
  + 从远程服务器拉取
+ push
  + 推送到子仓库，也可以设置推送到 哪个分支



### 例子

我们创建两个新的库分别是a和b。地址：https://github.com/yjua/a.git和https://github.com/yjua/b.git

目录内容如下：

```shell
// a.git
a
 |-- a.txt
 |-- README.md

// b.git
b
 |-- b.txt
 |-- README.md
```



我们把 `b.git` 添加到 `a.git` 的目录下。

我们在a.git的根目录下，执行如下命令。

```shell
git subtree add -P child https://github.com/yjua/b.git master
```

命令中的字符child 则表示字库存放的路径，即child文件夹下。master表示为子仓库的分支。

执行完之后我们在看`a.git`，目录如下。

```shell
a
|-- child
	|--b.txt
	|--README.md
|--a.txt
|--README.md
```



child文件跟`a.git` 中普通的文件是一样的。添加进来之后跟`b.git` 不存在关联关系。



#### 从子仓库拉取更新

如果`b.git` 更新了，使用git subtree pull 拉取更新。

```shell
# 在a.git 根目录下执行
git subtree pull -P child https://github.com/yjua/b.git master
```



#### 从父仓库推送到子仓库

如果我们在 `a.git` 中修改了 child 目录下的内容，然后想把修改的推送到 `b.git` 中。使用 git subtree push

```shell
#在a.git 根目录下执行
git subtree push -P child https://github.com/yjua/b.git master
```



### 同一个仓库不同分支

当我们在 `github`上使用 `hexo` 创建一个博客的时候，我们可能希望这么一个结构：源码在 `master` 分支上，然后把生成的 `dist` 文件夹内容推送到 `gh-pages`，作为用户访问的页面。

这个时候我们可以使用如下命令去做

```shell
git subtree push --prefix=dist origin gh-pages	
```

 命令就是把dist的目录推送到 `gh-pages` 分支下。

