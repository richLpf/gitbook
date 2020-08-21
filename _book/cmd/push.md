# push

git push 用来同步本地仓库和远程仓库版本

`git push [远程主机名] [本地分支名] [远程分支名]`

例如：`git push origin master:master ` 就是将本地分支master推送到远程主机origin的master分支



> 可以简写：省略远程分支名

`git push origin master` 标识要推送的分支名，本地和远程相同，一般情况下工作开发都是相同的。



__注意__

> 如果简写，省略本地分支名，代表*删除远程分支*，不会删除本地分支

`git push origin :dev` 标识推送了一个空的分支到远程的dev分支，本地的dev不会被删除

相同的命令 `git push origin --delete dev` 删除远程分支



> 省略本地分支和远程分支`git push origin`

1、查看远程主机可以使用`git remote -v`

2、如果当前分支和本地分支已经关联过，并且有多个远程，远程有多个相同的分支，可以增加主机名，判断推送哪个主机

3、如果关联远程主机，使用了`git push -u origin master`，-u默认指定了一个主机，之后推送不需要添加origin



> 同时省略远程主机，本地分支和远程分支

`git push`

如果当前分支只有一个远程分支，可以直接省略主机名。查看分支`git branch -r`



> 本地master分支和远程分支建立关联，默认指定主机

`git push -u origin master`



> 推送本地所有的分支到远程分支

`git push --all origin`



> 推送分支，我们需要先`git pull`拉取远程分支，同步代码或解决冲突，然后再push,不然git会提示你，如果一定要提交可以使用这个命令。不建议使用

`git push --force origin`



> 推送分支标签可以使用这个命令

`git push origin --tags`