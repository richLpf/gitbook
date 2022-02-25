# 一、Git分支命名和管理规范

## 1、Git命名规范

> Git由于分布式和通过指针定位版本的原理，带来的一个巨大优势就是新建分支的成本很低，既然如此，就要充分发挥分支的用途

首先，项目创建完成后会默认master分支（现在Github改成了main分支），作为生产环境的分支，特别重要的项目，这里可以设置为受保护的分支，只允许管理员操作，这样就能避免代码混乱，当然90%的代码估计都不太需要。

分支虽然不要钱，但审核代码的时间和找分支的时间是要钱的。如果随意起名字，后期修改代码很容易忘记，需要花时间回忆，所以规范分支命名是很必要的。

### （1）稳定分支

master/main分支：生产环境代码，记录每个版本迭代，保持分支稳定，不能直接修改。

develop分支：开发分支，开发功能需要从当前分支检出，保持稳定，最新版本。

### （2）不稳定分支

不稳定分支指的是更新频繁，并且有一定的生命周期。

release分支：预发布分支，当完成一个功能可以创建该分支用于测试，比如`release/login` `release/account` 

feature分支：功能分支，当需要开发一个功能时，可以创建该分支，开发上线后删除。`feature/login` `feature/register`

hotfix分支：生命周期最短，发现bug后临时建立，修复bug后删除。分支命名：`hotfix/login` `hotfix/register`

## 2、开发流程

开发流程和各分支的关系图：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/99a5822e5e0f475b9631fa1cf93e8353~tplv-k3u1fbpfcp-watermark.image)

例如：当需要开发新功能登陆页面的时候

- 从`develop`分支检出`feature/login`，经过一段时间开发完毕
- 需要发布版本测试，此时从`feature/login`创建`release/login`，部署该分支代码，进行测试
- 测试中的bug，在`feature/login`修复，然后合并`release/login`分支，提交第二个测试版本，第三个测试版本等
- 测试通过后，合并`release/login`到`develop`分支
- 确认没问题，合并master分支，部署上线
- 删除`feature/login` `release/login`分支

当需要修复线上登陆页面bug的时候

- 从`master`分支检出`hotfix/login`，修复完成，合并master分支，验证没问题
- 合并`develop`分支
- 删除`hotfix/login`分支

这里根据实际开发需求，对网上常见的工作流进行了修改，具体差别往下看

## 3、实际工作中

### （1）网上常见的分支管理和这里的区别

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7af50ca375c240d0b182581739edadf6~tplv-k3u1fbpfcp-watermark.image)

在查阅资料的过程中，发现很多分支管理如图所示，但在实际使用的过程中有个问题。

功能是不断迭代的，多人开发不同的功能，完成的先后顺序不一样，那么完成的功能合并到`develop`后再拉取预发布分支，容易将其他人的功能分支代码合并到一起，这样就会导致上线一些不应该上线的代码。

当然这里你也可以在不同的节点去处理，按版本号的方式检出预发布分支，但终究是容易混淆，因为很多版本的需求都是老板按心情提的，并不确定。

所以这里我首先不会去合并`feature`分支到`develop`，首先直接检出预发分支，然后测试迭代，最终没问题合并`develop`分支。

上线的时候可以选择合并`develop`到`master`也可以合并`feature`、`release`到`master`，都是可以的。

> 这里如果有不同的意见，欢迎交流。当然你也可以直接使用SourceTree获Gitlab等自带的分支管理策略。

### （2）阉割版的分支管理

其实上面的分支管理策略已经很优秀了。

但是对于很多公司内部的项目而言，可能没有那么正式的分类。

比如一个内部项目，多人同时开发，基本每个人负责一个功能，然后还要负责修复线上的bug，预发环境只有一个，可能同时测试，那么这时候，搞那么多的预发布分支，切来切去好麻烦。

这时候，我会推荐阉割版的分支管理办法

master 和 develop 分支作为稳定分支不变，其他的分支命名格式：`名字前缀/分支名称`

- 张三要开发登陆功能：`zs/feature-login`
- 张三要修获取用户信息bug：`zs/bugfix-userinfo`
- 李四要开发注册功能：`ls/feature-register`

这样每个人的分支就很清楚了，而且不容易误删，也能分辨分支的作用，缺点是分支名字有点长。



# 二、提交说明规范

提交信息随便写，什么`test`、`暂存了`，嗯，我也干过，但现在改了。优秀的信息说明要有一定的格式，方便查找历史提交记录，而且赏心悦目。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c29b4549e13e4ea692cf76fd56d902dc~tplv-k3u1fbpfcp-watermark.image)

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ea830690e212417aa7cc1e1c34fcb563~tplv-k3u1fbpfcp-watermark.image)

其实这些问题的解决并不难，只要态度到位，操作熟练加上点规范，分分钟钟变得优秀。

> 代码提交规范，其实重要性没那么高，只会在代码review和历史数据查询的时候使用，但规范的提交可以帮助你更好的记录代码的更新。

```
Subject 一句话概述commit主题(必须)
<Body> 详细描述 What 和 Why (可选)
<Footer>不兼容或关闭 issue 等说明(可选)

主题(Subject)是 commit 的简短描述，不超过50个字符
- 用一句话说明本次所作的提交, 如果一句话说不清楚，那有可能这个提交得拆分成多次
- 主要采用 Verb + Object + Adverb 的形式描述，常见动词及示例如下
1. Add: 添加代码和逻辑, 如 Add xxx field/method/class
2. Change: 代码更新，如 Change xxx to yyy with reason
3. Remove: 删除旧特性/功能，如 Remove xxx which was deprecated
4. Fix: 修复bug，如 Fix #123, fix xxx error
5. Update/Release: maven 版本变更， Update/Release xxx version to 1.0.0
6. Refactor: 代码重构, 如rename, move, extract, inline等
7. Polishing: 代码打磨(代码格式化，不涉及逻辑调整，使代码更清晰易读等无错修改)

正文(Body)详细描述本次 commit 做了什么、为什么这样做(不是怎么做的)
- 每行不要超过70字符
1. 这个改动解决了什么问题？
2. 这个改动为什么是必要的？
3. 会影响到哪些其他的代码？
  bug fix - 组件 bug 修复；
  breaking change - 不兼容的改动；
  new feature - 新功能

尾注(Footer) 用于关闭 Issue 或存在不兼容时添加相关说明等
1. breaking change: 与上一个版本不兼容的相关描述、理由及迁移办法
2. close #issue: 关闭相关问题（附链接）
3. revert: 撤销以前的commit
```

# 三、版本规范

我们通过Git Tag来管理项目的版本号，版本号基本都熟悉，这里简单介绍下。

具体工作中，版本很多时候除非有专门的管理，否则很容易就会被忽视，除非是哪些版本号很敏感的公开库或软件。

那么我们还是要讲下如何制定版本号。现在通用的版本号，主要是遵循语义化版本 2.0.0：

版本格式：主版本号.次版本号.修订号，版本号递增规则`X.Y.Z`如下：

1. 主版本号X：当你做了不兼容的 API 修改，
2. 次版本号Y：当你做了向下兼容的功能性新增，
3. 修订号Z：当你做了向下兼容的问题修正。

先行版本号及版本编译信息可以加到“主版本号.次版本号.修订号”的后面，作为延伸。

- X、Y、Z分别为非负整数，依次递增，向下兼容。

- X.Y.Z 版本号的更新如：
  - 小Bug迭代：`1.1.1 -> 1.1.2 `
  - 新增功能：`1.1.2 -> 1.2.0`
  - 大版本更新不兼容之前版本：`1.2.1 -> 2.0.0`
- X 如果为0则代表当前版本为开发阶段，1则为第一个公开版本，X更新Y和Z从0开始计数，Y更新Z要从0开始。
- 最后在X.Y.Z后面加上短线和说明来扩展特殊的版本
  - alpha: 内部版本，例如`1.1.0-alpha.1`，代表版本号为1.1.0的第一个内部测试版本
  - beta: 测试版本，例如`1.1.0-beta.1`，代表版本号为1.1.0的第一个公开测试版本
  - RC(Release Candidate)，例如：`1.1.0-rc1`，代表版本号为1.1.0的第一个预发布版本
  - stable: 稳定版本，例如`1.1.0-stable`，代表这是一个稳定版本

其实上面这些版本都已经完全足够你使用了，但是维护版本号确实很分神费力的工作。

这里我们先在实际项目中展示一个比较简单的方案，来管理（所有）前端项目版本号，即将版本号工程化。



# 四、Git的钩子检测

Git的钩子函数，是我们后面用来做自动化很方便的一个部分，这里配置一下，为后面铺路。

Git Hooks在每次Git命令执行前，允许我们做一些操作。





# 五、一个简单的前端版本管理方案

通过API服务，每次发布版本，更改项目的版本号，然后同步更新API服务，就完成了版本的更新。





# 六、参考文章：

- [Git文档](https://git-scm.com/book/zh/v2)
- [我在工作中是如何使用git的](https://juejin.cn/post/6974184935804534815#heading-18)
- [您必须知道的 Git 分支开发规范](https://juejin.cn/post/6844903635533594632)
- [Git Hooks](https://zhuanlan.zhihu.com/p/149294652)
- [阮一峰分支管理]



# 七、准备工作

安装Git

提前准备两个一个项目