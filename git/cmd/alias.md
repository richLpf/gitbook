# alias

## git 别名

> 可以通过给git起别名的方式，减少git命令的输入

`git config --global alias.br branch` branch -> br

查看分支`git br -a` 等价于 `git branch -a` 

`git config --global alias.ci commit` commit -> ci

提交版本 `git ci -m 'test'` 等价于 `git commit -m 'test'`

`git config --global alias.st status` status -> st

查看文件状态 `git st` 等价于 `git status`

### 基于git别名，我们可以自定义一些常用的命令

1、`git checkout -b newbranch`

创建一个新分支，并检出到工作区

`git config --global alias.new "checkout -b"`

执行命令`git new newbranch` = `git checkout -b newbranch`

2、自定义看到最新的一次提交

原命令：`git log -1`

`git config --global alias.last "log -1"`

执行命令`git last` = `git log -1`

当然git的命令已经很简单了，alias的作用也可以是你觉得和自己习惯不匹配的用法，可以自行定义，这就很优秀了。


