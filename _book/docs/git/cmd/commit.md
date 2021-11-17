# git commit

提交暂存区的文件到本地仓库，是原子提交，每次提交版本都会在本地库生成一个40位的哈希值，我们也叫commit-id

> 之后我们可以通过commit-id 回退到某个节点，或者从某个commit-id 拉取分支

git commit 使用在git add 之后，是将暂存区的内容一次提交到本地版本库，如果暂存区没有内容，则无法提交

`git commit -m "first commit"` 最常用

通常我们都用上面这个命令来提交代码，-m 后面带上本地提交的描述信息

`git commit`

省略参数，则会进入命令交互行，git会要求你输入描述信息，此时你可以看到要提交的文件，可以随意输入多行的描述

`git commit -a -m "message"`  （慎用）

-a 将工作区和暂存区git追踪的文件提交到版本库，这里的提交不包括新增的文件，因为新增的文件还没有被git管理

这样就可以直接将本地的修改提交到版本库，省略了git add 这一步操作

工作中还是建议先通过`git add` 提交到暂存区，再进行操作。


`git commit -amend`

该命令需要实践，具体见：

https://www.cnblogs.com/wangrenmeng/p/10430369.html

`git commit --help`

注意：commit提交信息可以换行，方法有：

1、`git commit` 进入命令行编辑换行

2、
```
git commit -m "第一行
第二行
第三行"
```


