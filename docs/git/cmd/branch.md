# branch

分支是Git的一大优势，有了Git分支，同一个项目可以多人同时开发，而互不干扰。而且Git的创建分支特别容易，本地指针直接改变指向，就可以创建分支,所以可以尽情的创建分支，然后开发。

git branch

查看本地分支

git branch -a 

查看所有分支(包括远程分支吗？)

git branch -r 

查看远程分支

git branch -l


git branch dev  

新建分支dev


git checkout dev

检出存在的分支，如果不存在检出失败，需要创建分支


检出远程分支


新建分支并和远程仓库建立联系


从某个提交版本拉取分支


从某个分支拉取分支


git branch -d dev

删除dev分支，只能删除本地分支

git branch -v 

查看每个分支最后一次提交


git branch -a || --all

git branch -l || --list

git branch

git checkout -b dev



