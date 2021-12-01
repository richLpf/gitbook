# Gitbook博客搭建



> 通过Github、GitBook、Git Actions、utools图床插件 和 Typora 打造自己的博客系统



## 一、前言

现在有一个个人博客其实很简单，注册个博客网站的账号就可以了。

如果有自己的服务器啥的，可以通过wordpress搭建一套也很快。

如果想免费，也可以通过Github和Gitee等搭建自己的博客。

这里介绍就是通过Github和GitBook搭建一套一个免费的博客。



## 二、使用到的技术和工具

- [GitBook账号](https://www.gitbook.com/)：其实只要一个GitBook就可以在线创建自己的博客或者电子书了

- [Git](https://git-scm.com/)：用来管理项目，默认安装

- Node环境：默认安装

- [gitbook](https://www.npmjs.com/package/gitbook)：gitbook项目脚手架

- [Github仓库两个项目](https://github.com/)：一个用来放GitBook项目，一个用来做图片仓库

- 图床工具：博客中一般都有图片，但是上传Git太麻烦，所以使用工具，选一个就可以了

  - [在线图床](https://picx.xpoet.cn/#/management)
  - [utools工具](https://u.tools/)

- [Typora-Markdown编辑器](https://typora.io/dev_release.html)：1.x版本要收费了，可以下载0.x版本

- Github Actions

  

## 三、项目展示

### 1、本地编辑

![本地编辑](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1638343355358Typora-picture.png)



### 2、Git提交，自动执行Git Actions

![github Actions](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1638343411641action.png)



### 3、打开个人博客

![个人博客](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1638364574867blog.png)



## 四、实际部署

- 1、GitBook账号

- 2、[GitBook 脚手架使用](gitbook/gitbook-cli.md)
- 3、用Github联动GitBook
- 4、配置Github Actions
- 5、图床配置和使用
- 6、配置Github Page
- 7、其他博客
  - Hexo
  - Vue-press