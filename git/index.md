## 查看配置
```shell
git config --list
git config -h
git config user.name
git config user.email
```



## 查看记录

```powershell
git log
```



## 提交代码

```shell
git commit

# 合并提交记录，该命令将文件往最后一次提交的记录上合并，不会产生新的提交记录
git commit --amend 
```



## 分支管理

```shell
# 查看分支列表
git branch

# 修改分支名
git branch -m [现有分支名] [修改后的分支名]

#删除分支
git branch -D
```



## checkout

```shell
# 新建分支
git checkout -b [分支名]

# 撤销该文件的修改回复到上一个版本状态
git checkout filename

# 切换分支
git checkout [分支名]
```



## 合并分支

```shell
git rebase 
git merge [分支名]

# 区别：merge命令不会保留merge分支的commit

```



## 回退版本

```shell
# 回退到以前版本
git revert Head

#回退到某个版本，但是可以看到最新的版本，只是将Head指针放到最前面
git cherry-pick Head

# 回到某个版本，无法查看最新版本
git reset --hard Head
```

