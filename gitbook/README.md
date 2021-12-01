# Gitbook博客搭建



> 通过Github、GitBook、Git Actions、utools图床插件 和 Typora 打造自己的博客系统



## 相关技术

- github
  - github actions
- gitbook
  - 自动同步Github代码
- utools
- github作为图床
- Typora作为编辑器（vscode）



## 截图展示



### 1、本地编辑

![本地编辑](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1638343355358Typora-picture.png)



### 2、Git提交，自动执行Git Actions

![github Actions](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1638343411641action.png)



### 3、打开个人博客

![博客](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1638343347157gitbook.png)



## gitbook-cli安装

```
npm i -g gitbook-cli

gitbook -V
```


## 创建项目

```
mkdir git-learn

cd git-learn
```



## 打包构建

gitbook build 默认生成_book下

gitbook build ./ ./docs



## 生成目录

```
gitbook init

gitbook serve

```
__目录结构见SUMMARY.md__



## 关联gitbook网站，生成网站效果

GitBook 关联



## gitbook目录结构


如何在markdown中打印项目目录结构
https://www.jianshu.com/p/e38a07f824a2


Gitbook各种使用方法参考文章

https://juejin.im/post/6844903793033740302

Gitbook使用方法

https://yangjh.oschina.io/gitbook/faq/Plugins.html

gitbook插件参考

https://www.jianshu.com/p/427b8bb066e6

可参考优秀的文档

https://snowdreams1006.gitbook.io/www/#ke-long-wang-zhan