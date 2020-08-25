# 简单介绍

## 初衷

最近辅导刚入职的毕业新生，发现刚开始工作的朋友对于git简直是迷茫的不行。

许许多多的坑还在等着他们去踩，想起自己以前刚接触git的时候，对于git系列的痛苦也是很心酸。

所以在工作之余绝对写下这个文档，并且尝试录制视频来讲解下git相关的内容，希望可以帮助到一些刚刚接触到git的同学。

## 关于文档

git对于大多数程序员来说，基本是天天要使用的工具，所有对有工作前提前掌握git的小伙伴来说是可以节省很多时间的，对于天天使用git的小伙伴来说，很多时候我们仅仅用了git一部分功能，到遇到回退、配置、分支管理等解决一些具体的问题时，还是要不断的查命令去解决，从这个角度来说我们还需要学习很多。

思维导图：确定项目结构


## 项目目录结构


## 项目简介

## 增加项目更新进度

## 预计更新内容

git基础
- git工作快速上手
- git基础
- git配置
- git基本命令
- git工作中的使用
- git常用操作
- git原理

git远程仓库

- github
- gitlab
- gitee

图形化工具
- sourceTree
- smartGit

git分支管理
- git分支管理

gitbook的使用

## 文档操作

gitbook-cli安装

```
npm i -g gitbook-cli

gitbook -V
```
创建项目

```
mkdir git-learn

cd git-learn
```

生成目录

```
gitbook init

gitbook serve

```

安装插件

```
//book.json文件，见根目录
touch book.json

npm install gitbook-plugin-[插件名]
```