# tag

git tag

## 查找标签

`git tag -l/--list`

`git tag -l "v1.*"` -l 可以增加匹配模式

`git show v1.0.0` 查看特定标签

## 增加标签

### 附注标签

`git tag -a v1.0.2 -m "my version is 1.0.2"`

可以显示打标签的信息，打标签的日期，附注信息，具体的提交信息

### 轻量标签

`git tag "v1.0.0"`

git show 仅仅提供具体的提交信息

### 在某个提交commit-id补打标签

1、首先找到对应的commit-id

`git log --oneline`

找到一个commit-id 1502345

2、`git tag -a v1.0.3 1502345` 

3、`git show v1.0.3`

查看标签信息

### 推送分支（共享分支）

`git push origin v1.0.3`

推送特定分支到远程仓库

`git push origin --tags`

推送所有标签到远程仓库

## 检出分支，并修改

`git checkout v1.0.3`

注意，此时git无法确定分支名称，处于“分离头指针”的状态，如果你修改当前代码，提交后只能通过commit-id找到对应的修改，所以是有问题的。具体可以使用下面的方法：

`git checkout -b version1 v1.0.3`

要修复某个标签的代码bug,可以从该标签检出，并创建新得分支，进行修改，修改完后合并上线。

## 删除分支

1、方法一语法：`git tag -d <tagname>`

`git tag -d v1.0.3` 

本地删除标签名称v1.0.3,并不会同步远程仓库，需要继续执行命令

`git push origin :refs/tags/v1.0.3`

2、方法二,类似删除分支的方法：

`git push origin --delete/-d <tagname>`

`git push origin -d v1.0.3`




