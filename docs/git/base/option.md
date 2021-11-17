# 项目实战

这里我们通过实际讲解创建一个git仓库，并关联远程仓库的几种方法

前提：
- 系统安装了Git
- 通过`git config`命令配置了用户名和邮箱
- 配置了ssh用户权限，具体配置方法见：（远程仓库关联，权限配置）

## 方法一：

> 新建一个本地仓库 + 关联远程仓库

新建文件夹

```
mkdir gitDemo/获取一个存在的目录，例如gitDemo

cd gitDemo

git init

```
这样就创建了一个本地仓库

在本地的项目中，添加文件

```
touch index.js

echo "test" >> index.js

```
提交到本地版本库

```
git add .
git commit -m 'first commit'
```
关联远程仓库

本地仓库默认的分支为master

具体看章节github，创建并关联本地仓库


## 方法二：

> 远程仓库已存在远程仓库，拉取到本地

1、_http的仓库地址一般可以直接下载_

比如现有仓库 https://github.com/richLpf/golang-learn.git

我们只需要通过`git clone https://github.com/richLpf/golang-learn.git`就可以将仓库下载到本地，golang-learn目录就是项目仓库。

如果想要改名字可以这样
```
git clone https://github.com/richLpf/golang-learn.git newName
```
这时候下载的newName就是项目仓库



2、__git@通过ssh key连接的项目，一般要先配置项目权限__

一般在管理员Setting页面Deploy keys将本地Git生成的SSH key放在这里，【生成方法见】

配置完成后，执行类似一的 `git clone git@github.com:richLpf/golang-learn.git`


