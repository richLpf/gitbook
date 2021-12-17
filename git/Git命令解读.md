# Git命令解读

## 一、Git Push

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

3、如果关联远程主机，使用了`git push -u origin master`，__-u默认指定了一个主机__，之后推送不需要添加origin



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



## 二、Git Tag

>  查找标签

`git tag -l/--list`

`git tag -l "v1.*"` -l 可以增加匹配模式

`git show v1.0.0` 查看特定标签



> 增加标签、附注标签

`git tag -a v1.0.2 -m "my version is 1.0.2"`

可以显示打标签的信息，打标签的日期，附注信息，具体的提交信息



> 轻量标签

`git tag "v1.0.0"`

git show 仅仅提供具体的提交信息



> 在某个提交commit-id补打标签

1、首先找到对应的commit-id

`git log --oneline`

找到一个commit-id 1502345

2、`git tag -a v1.0.3 1502345` 

3、`git show v1.0.3`



> 推送分支（共享分支）

`git push origin v1.0.3`

推送特定分支到远程仓库

`git push origin --tags`

推送所有标签到远程仓库



> 检出分支，并修改

`git checkout v1.0.3`

注意，此时git无法确定分支名称，处于“分离头指针”的状态，如果你修改当前代码，提交后只能通过commit-id找到对应的修改，所以是有问题的。具体可以使用下面的方法：

`git checkout -b version1 v1.0.3`

要修复某个标签的代码bug,可以从该标签检出，并创建新得分支，进行修改，修改完后合并上线。



> 删除分支

1、方法一语法：`git tag -d <tagname>`

`git tag -d v1.0.3` 

本地删除标签名称v1.0.3,并不会同步远程仓库，需要继续执行命令

`git push origin :refs/tags/v1.0.3`

2、方法二,类似删除分支的方法：

`git push origin --delete/-d <tagname>`

`git push origin -d v1.0.3`



## 三、Git Add

> 作用，将修改的文件从工作区提交到暂存区

1、`git add .` 

`git add --all` 简写 `git add -A`

提交所有变化，包括工作区修改的、新增的、删除的文件都会添加到暂存区

2、`git add -u`  简写 `git add --update`

提交修改的、删除的文件，不包括新增的文件


3、`git add [file...] `

提交file文件到暂存区。

例如：`git add index.html index.js` 提交index.html, index.js文件

4、`git add -i`

git add 的交互式命令，输入后可以返回

```
*** Commands ***
  1: status       2: update       3: revert       4: add untracked
  5: patch        6: diff         7: quit         8: help
What now>

```

- 1、status拉取文件状态，和进入交互界面显示相同
- 2、update可以选择修改的文件提交到暂存区
- 3、revert,将提交到暂存区的文件，恢复到工作区。
- 4、add untracked将新建的文件，添加到暂存区
- 5、patch 未测试出来，待测试
- 6、diff 详细查看文件的修改
- 7、quit 关闭交互命令行
- 8、help 帮助文档

5、`git add [file] --edit|-e`

打开修改的文件，编辑文件

6、`git add [file] --force|-f`

强制提交被忽略的文件（强烈不建议使用） 

7、`git add [file] --dry-run|-n`

8、`git add [file] --verbose|-v`



## 四、Git Alias

### 1、git 别名

> 可以通过给git起别名的方式，减少git命令的输入

`git config --global alias.br branch` branch -> br

查看分支`git br -a` 等价于 `git branch -a` 

`git config --global alias.ci commit` commit -> ci

提交版本 `git ci -m 'test'` 等价于 `git commit -m 'test'`

`git config --global alias.st status` status -> st

查看文件状态 `git st` 等价于 `git status`



### 2、基于git别名，我们可以自定义一些常用的命令

1、`git checkout -b newbranch`

创建一个新分支，并检出到工作区

`git config --global alias.new "checkout -b"`

执行命令`git new newbranch` = `git checkout -b newbranch`

2、自定义看到最新的一次提交

原命令：`git log -1`

`git config --global alias.last "log -1"`

执行命令`git last` = `git log -1`

当然git的命令已经很简单了，alias的作用也可以是你觉得和自己习惯不匹配的用法，可以自行定义，这就很优秀了。



## 五、Git Log

> Git log 查看Git提交历史

1、`git log` 将会展示

```git
commit c0749c8ff4eb9287f69fecaeb7105cb1b4f59d10
Author: richLpf <lpengfei66@163.com>
Date:   Thu Jul 16 10:05:01 2020 +0800

    task

commit efd9b2347c8a67cf49b865845a216f13edcc6faa
Author: richLpf <lpengfei66@163.com>
Date:   Thu Jul 16 10:03:41 2020 +0800

    test
```

我们可以看到commitid, Author, Date和提交的注释信息

2、git log -[num]

示例：`git log -2`

展示两条提交日志

3、`git log -p` 或 `git log --patch`

展示提交的文件修改的详细信息

4、`git log --stat`

5、`git log --oneline` 

6、`git log --after="2020-15-05"`

7、`git log --after="2020-15-05" --before="2020-25-05"`

`git log --since=2.weeks`  `--until` == `--before`

时间还可以是：yesterday, today, 10 day ago, 1 week ago, 2 month ago

8、git log --author="pengfei"

9、过滤`git log --grep="test"`

10、忽略大消息查询 `git log -i --grep="test"`

11、过滤多个参数，查询参数中间使用`\|` 连接 `git log --grep="test\|测试"`

12、查询单个文件提交记录 `git log index.html`  多个文件 `git log index.html index.js`

13、`git log -S"function compare"` 查找文件中的某段代码添加的提交记录

14、`git log --merges` 查看当前分支合并的记录

15、`git log master..dev` 

16、定义打印日志格式 

> `git log --pretty=format:"%Cred%an - %ar%n %Cblue %h -%Cgreen %s %n"`
> `git log --pretty=oneline`

选项 说明
%H 提交的完整哈希值
%h 提交的简写哈希值
%T 树的完整哈希值
%t 树的简写哈希值
%P 父提交的完整哈希值
%p 父提交的简写哈希值
%an 作者名字
%ae 作者的电子邮件地址
%ad 作者修订日期（可以用 --date=选项 来定制格式）
%ar 作者修订日期，按多久以前的方式显示
%cn 提交者的名字
%ce 提交者的电子邮件地址
%cd 提交日期
%cr 提交日期（距今多长时间）
%s 提交说明

可以使用red等颜色

17、`git log --graph` 图形化展示各分支的提交历史

18、`git log --no-merges` 仅展示提交的版本，不展示合并分支的提交

注意：这些命令可以相互组合，下面通过几个例子来灵活的使用这些命令

1、仅展示两条数据

2、查看index.html文件的提交记录，并查询修改
