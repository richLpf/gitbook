# git add

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
1、status拉取文件状态，和进入交互界面显示相同
2、update可以选择修改的文件提交到暂存区
3、revert,将提交到暂存区的文件，恢复到工作区。
4、add untracked将新建的文件，添加到暂存区
5、patch 未测试出来，待测试
6、diff 详细查看文件的修改
7、quit 关闭交互命令行
8、help 帮助文档

5、`git add [file] --edit|-e`

打开修改的文件，编辑文件

6、`git add [file] --force|-f`

强制提交被忽略的文件（强烈不建议使用） 

7、git add [file] --dry-run|-n

8、git add [file] --verbose|-v

