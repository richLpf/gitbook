这是我参与更文挑战的第7天，活动详情查看：[更文挑战](https://juejin.cn/post/6967194882926444557)

# 一、前言

看到掘金上，一篇Git相关的文章一千多个点赞把我羡慕的呀。

Git在工作中是很重要的一部分，如果操作不熟练或者使用不规范，很容易给工作造成很多麻烦
- 比如习惯所有功能写在一个分支，导致无法分开上线
- 比如提交了依赖目录，导致Git仓库的代码过大
- 比如合并分支出现错误，将不用上线的代码提交
- 比如分支命名不规范，导致误删分支

这一篇正好是前端工程化实践的一部分，既然重合了，就写点不一样的内容，基础的就不再过多的写。这篇文章会主要聊聊以下几个方面：
- Git的一些使用场景
- Git分支命名推荐
- Git多人开发分支管理
- Git记录提交规范
- 推荐Git的图形化界面SourceTree及使用方法

# 一、新同学使用Git常见的一些问题

最近在帮助新同学上手项目的时候，发现他们使用Git时，会有一些小问题，主要是不熟悉，这里提出来讨论下。

## 1、问题一：对Git概念、操作不熟悉
都见过这样的新手，刚开始启动项目，写好代码后给导师审核，竟然直接压缩代码发给导师，每更新一个版本就打一个包，这就很狂了。

这是典型的对Git不熟悉，有必要通过多看文档，强制自己使用来掌握。其实知道了原理，Git常用的就那几个命令，并不难。

## 2、问题二：代码合并、冲突和保存代码时没有利用好Git的强大特性

代码合并，容易搞错合并的方向，`git merge`到底是哪个分支合并到哪个分支，这个是易错点。

合并的时候容易失败，出现冲突或是没有拉取线上最新的代码，导致当前header落后于master，所以合并前一定要先`git pull`。

## 3、问题三：分支随便建，信息乱提交

分支虽然不要钱，但审核代码的时间和找分支的时间是要钱的。如果随意起名字，后期修改代码很容易忘记，需要花时间回忆，所以规范分支命名是很必要的。

提交信息随便写，什么`test`、`暂存了`，嗯，我也干过，但现在改了。优秀的信息说明要有一定的格式，方便查找历史提交记录，而且赏心悦目。

## 4、请问你觉得下面哪个代码提交更专业?

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c29b4549e13e4ea692cf76fd56d902dc~tplv-k3u1fbpfcp-watermark.image)

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ea830690e212417aa7cc1e1c34fcb563~tplv-k3u1fbpfcp-watermark.image)

其实这些问题的解决并不难，只要态度到位，操作熟练加上点规范，分分钟钟变得优秀。

# 二、Git使用过程中的常见问题

Git一款分布式版本管理工具，本地安装Git，通过各种命令可以对代码进行拉取、合并、提交等，他也是程序员的基本技能。svn（小污龟）就不说了，写代码用Git准没错。

## 1、`.gitignore`的配置规则，允许使用正则

`.gitignore`文件可以让git提交的时候，过滤一些不需要提交的文件或文件夹，比如打包的文件、依赖安装包、编辑器的配置等。具体的过滤规则有：
- /build/ 不提交整个文件夹
- *.zip 过滤所有.zip文件
- /docs/do.txt 过滤某个具体文件
- !src/   不过滤该文件夹

具体语法：
1. 以斜杠/开头表示目录；
2. 以星号*通配多个字符；
3. 以问号?通配单个字符
4. 以方括号[]包含单个字符的匹配列表；
5. 以叹号!表示不忽略(跟踪)匹配到的文件或目录；
从上到下执行，下面的命令会覆盖前面的

## 2、当前分支功能开发一半，需要终止修复线上Bug，怎么办？

通过`git stash`指令，保存临时修改的代码到本地，该操作将清空工作区，然后切换分支去处理bug，完成后切回原来的分支，通过`git stash`指令还原之前的修改，而不是直接提交写一半的代码到暂存区。

## 3、git冲突处理

在合并代码的时候出现冲突很正常，合并冲突也很简单，但也很容易搞混，那么出现了冲突，我一般会通过VSCode去解决，现在我们使用两个分支同时改一个地方，然后合并代码制造冲突，并解决它。
![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2afdc92a289f4ae4afdd65a0fb6069ee~tplv-k3u1fbpfcp-watermark.image)

可以看到出现的冲突，在VSCode中看到提示，然后选择上面的选项处理冲突，如果两者对比不明显还可以选择Compare Changes，两栏对比
![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ccd6da2a367e4c4daef76e1587f02554~tplv-k3u1fbpfcp-watermark.image)

## 4、vscode安装插件，实时查看每行代码的修改信息
安装插件：GitLens — Git supercharged，可以展示如下图信息，修改人，修改时间，提交信息，这样就能很清晰的找到代码的作者
![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/71a34633a0434fed884b3b38958ddffc~tplv-k3u1fbpfcp-watermark.image)

# 三、Git命名和管理规范

## 1、Git命名规范

> Git由于分布式和通过指针定位版本的原理，带来的一个巨大优势就是新建分支的成本很低，既然如此，就要充分发挥分支的用途

首先，项目创建完成后会默认master分支（现在Github改成了main分支），作为生产环境的分支，特别重要的项目，这里可以设置为受保护的分支，只允许管理员操作，这样就能避免代码混乱，当然90%的代码估计都不太需要。

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

# 三、Git信息提交规范

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

# 四、Git图形化工具推荐

> Git有很多优秀的图形管理工具，比如SourceTree和SmartGit，这些工具帮助我们更容易对分支和更新内容进行操作，这里强烈安利这款工具，帮你不必使用Git命令行工具，一行行的输入命令，通过界面点击就可以实现这些操作命令。

这里我推荐SourceTree，他有以下优缺点：
- 首先免费，其次操作简单。
- 展示内容详细，实时监控代码的更新，不论远程仓库还是本地的变更
- 可以一个代码块一个代码库进行回滚操作
- 展示提交的历史信息和各个分支之间的关系
- 安全，不允许你删除没有合并的分支，除非你强制删除
- 缺点也有：对比命令行，有点慢，但不影响使用，毕竟他执行的命令更详细。

## 一、安装

下载地址：[SourceTree](https://www.sourcetreeapp.com/)

## 二、配置用户信息

- 1、配置用户名和电子邮件地址，作为后面提交代码的信息展示
- 2、SSH客户端配置

首先需要本机安装`Git`，并生成`key`，[具体生成方式](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E7%94%9F%E6%88%90-SSH-%E5%85%AC%E9%92%A5)和服务器命令行一样。

__生成key的方法这里有两种，不同系统有差别，都可以使用Git bash操作__

- 1、通过`Git`命令行生成，具体过程可以参考章节Git生成`sshkey`
- 2、通过`SourceTree`，`ssh`助手生成`key`

通常Windows的key都在`User\user.ssh\id_rsa`下，选择协议使用OpenSSH

Linux电脑的key一般存在`root/.ssh` 目录，其中一个公钥一个私钥。

Mac的key一般存在`/home/.ssh`目录

## 三、创建或引入一个项目

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/552384f2182a4d3b95d1f85159ffb453~tplv-k3u1fbpfcp-watermark.image)

- 1、添加已经存在的本地仓库，将本地的Git仓库用SourceTree管理起来
- 2、创建远程仓库，SourceTree 帮助你直接创建远程仓库
- 3、从URL克隆，相当于`git clone`，只要有项目地址就可以将项目clone到本地
- 4、添加本地已经存在的本地仓库，如果你本地有个git项目，那么你可以直接引入
- 5、创建本地仓库，选择一个不是仓库的文件夹，创建一个git仓库，相当于git init
- 6、这样我们就可以通过SourceTree使用git命令操作项目了。
- 7、引入成功后，是这个样子

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e18bf68c42a9485492b913b4d2cfea82~tplv-k3u1fbpfcp-watermark.image)

## 四、工作区域说明

SourceTree 整个界面分成了多个区域，这里我们重点介绍下这6个区域，也是我们常用的地方

> 第一个工作区，最上面的部分，主要是Git的常用操作，包括

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4921a0c2f0994ddabf4d314de70c4fef~tplv-k3u1fbpfcp-watermark.image)
- 提交（git add）
- 拉取（git pull）
- 推送（git push）
- 获取（git fetch）
- 分支（git branch、git checkout、git push -d branch）
- 合并（git merge）
- 贮藏（git stash）
- Git工作流，主要是自动创建不同的分支流程，帮助多人合作开发管理分支，类似我们章节分支管理中的内容
- 远端：点击展示当前项目远程仓库信息，提供git remote -v 和修改删除的功能
- 命令行模式，window下快速打开bash命令行
- 资源管理器，一键打开项目所在的文件夹目录
- 设置远程仓库配置和Git配置信息设置

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0a938c75280048deaae1db17ffb0f528~tplv-k3u1fbpfcp-watermark.image)

这个区域有个很厉害的地方，只要线上代码更新，就会提示你当前版本落后的版本数，提醒你及时拉取代码，而不用每次都去使用命令拉取一下。

> 第二个工作区(WORKSPACE 工作区信息)
- 文件状态，主要展示当前项目你追踪的修改、删除的文件或未追踪，新建的文件，对应的命令`git status`
- 历史, 暂存文件提交的历史和说明，分支合并的路径等信息 对应的命令 `git log`， `git grash`
- 搜索 提供了对`git log`的封装

> 第三个工作区

- 分支：使用的命令包括 `git branch -a`，可以很直观的看到当前创建的分支
- 标签：展示所有的标签内容
- 远程分支：远程仓库所有的分支
- 贮藏区：相当于`git stash`，保存在本地的临时代码

> 第四个区域

主要配置，根据各个Tab切换展示不同的内容，筛选日志数据，文件状态分区，清晰的展示了各分支之间的关系和来龙去脉

同时对于修改的内容也会实时获取，这个就很方便了，比如你改动了一些代码，不需要在打开Gitlab网页，直接就能在这里看到。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fac8313df028490da8d5cb1859da65a6~tplv-k3u1fbpfcp-watermark.image)

出现冲突的界面

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cb7dc6a256c94bc6826154aff3c5b9bd~tplv-k3u1fbpfcp-watermark.image)

合并冲突会直接显示黄色感叹号。

> 第五工作区

上面主要展示了，`git logs` `commit-id`和两个版本之间的修改内容

> 第六个工作区

主要展示某个`commit-id`修改内容的差异
如上图，右边还可以通过回滚区块，来操作小块代码的回滚，非常方便

**注意：mac和window系统的SourceTree在界面上有一些不同**

# 五、参考文章：
- [Git文档](https://git-scm.com/book/zh/v2)
- [我在工作中是如何使用git的](https://juejin.cn/post/6974184935804534815#heading-18)
- [您必须知道的 Git 分支开发规范](https://juejin.cn/post/6844903635533594632)
- [阮一峰分支管理](https://www.ruanyifeng.com/blog/2012/07/git.html)