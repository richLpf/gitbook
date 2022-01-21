## 一、初始化项目

安装node，安装hexo

```
node -v
npm install hexo -g
```

初始化项目

```
// 想初始化
hexo init 
//运行
hexo serve 
// 生成新的文章
hexo new "new passage"
// 生成html文件
hexo generate
// 部署远程服务器
hexo deploy
```

## 二、部署github

```
npm install hexo-deployer-git -S
hexo clean
hexo deploy
```

*_config.yml配置*

```
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: <你的仓库地址> # https://github.com/TJ-XiaJiaHao/TJ-XiaJiaHao.github.io
  branch: master
```

访问站点: [github pages 设置](https://richlpf.github.io/lpf.github.io/index.html)

注意：

- 1、后面要加上index.html,访问首页
- 2、可以拉取代码，配置访问二级目录

## 三、部署Gitee

如果向我一样是二级目录，则需要修改根目录下`_config.yml`

```
# URL
## Set your site url here. For example, if you use GitHub Page, set url as 'https://username.github.io/project'
url: https://pengfeilv.gitee.io/blog
root: /blog
```

这里url访问的根路径就是二级目录，但css等文件是根据二级目录相对的，所以这里需要手动更改/blog，确保项目访问的静态文件路径正常

## 四、安装问题

静态资源无法加载问题

```
https://blog.csdn.net/weixin_44474742/article/details/97271608

hexo deploy 上传超时 问题

git config –global credential.helper store
```

## 五、插件系列

### 1、插件配置

```
package.json
"dependencies": {
    "hexo": "^3.9.0",
    "hexo-deployer-git": "^2.1.0",
    "hexo-generator-archive": "^0.1.5",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-feed": "^2.2.0",
    "hexo-generator-index": "^0.2.1",
    "hexo-generator-json-content": "2.2.0",
    "hexo-generator-search": "^2.4.0",
    "hexo-generator-tag": "^0.2.0",
    "hexo-renderer-ejs": "^0.3.1",
    "hexo-renderer-jade": "^0.3.0",
    "hexo-renderer-marked": "^2.0.0",
    "hexo-renderer-stylus": "^0.3.3",
    "hexo-server": "^0.3.3",
    "hexo-wordcount": "^6.0.1"
}
```

### 2、插入音乐或视频

```
https://mp.weixin.qq.com/s?__biz=MzU3MDI4NTM5Mg==&mid=2247485106&idx=3&sn=50a8681007d7b82a29c92e70110fff1d&chksm=fcf08a8dcb87039b272837b93f54db6bed8f3401f7d65f6caf9cec28edb1119fdfee82d41&scene=21#wechat_redirect
```

### 3、搜索功能

### 4、自定义404页面

```
https://mp.weixin.qq.com/s?__biz=MzU3MDITM5Mg==&mid=2247485262&idx=3&sn=3e2aff5d7fb4979ca70d275bae48525f&chksm=fcf08b7870267c972b11b1e5f0c345af39170c756de170adde8e70532360a68debcb0b3c4&scene=21#wechat_redirect
```

### 5、百度seo

```
yarn add hexo-baidu-url-push

https://www.jianshu.com/p/f37452d4978e
```

### 6、文章字数，阅读时长统计

```
hexo-wordcount
```

### 7、评论功能

```
leancloud-storage –save
```

### 8、评论提醒

[gitalk评论系统](https://blog.csdn.net/qq_36537546/article/details/90730412)

## 六、尝试参考案例优化

```
https://zhuanlan.zhihu.com/p/157391730
https://hexo.io/zh-cn/docs/github-pages
```

## 七、待优化问题

自动在对应的目录通过命令生成文件

目前生成目录在_post目录下，应该生成在_post/blog下面

```
hexo new "page title"
```

从零开始搭建hexo,这是入门篇，主要介绍下hexo的初始化项目，之后慢慢记录hexo的进阶。

常见博客案例：

- hexo博客展示

![hexo](https://gitee.com/pengfeilv/picture-manage/raw/master/blog/hexo-page.png)