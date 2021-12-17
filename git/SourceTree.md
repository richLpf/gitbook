## git图形化工具推荐

> git有很多优秀的图形管理界面，就想window可以帮助更多的小白通过点点点，操作电脑。git的图形界面也是这样的，帮你不必使用git命令行工具，一行行的输入命令，通过图形点击就可以实现这些操作命令。

常见的我用过的有**SourceTree**, **smartGit**
这里我主要推荐SourceTree,首先免费，其次操作简单，内容展示比较全。

![TPL_中台产品部 > Git图像化工具使用 > 1.png](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639709636022sourcetree1.png)

### 一、安装

下载地址：[SourceTree](https://www.sourcetreeapp.com/)

具体安装过程省略，可能需要翻墙。

### 二、配置

工具栏 -> 选项

![TPL_中台产品部 > Git图像化工具使用 > gitconfig.png](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639709641194sourcetree2.png)

- 1、填写用户名称和电子邮件地址，作为后面提交代码的信息展示
- 2、SSH客户端配置
  首先需要电脑安装git，并生成key,

**生成key的方法这里有两种**

- 1、通过git命令行生成，具体过程可以参考章节git生成sshkey
- 2、通过sourceTree，ssh助手生成key
  通常windows电脑的key都在User\user.ssh\id_rsa下，选择协议使用OpenSSH

linux电脑的key一般存在root/.ssh 目录，其中一个公钥一个私钥。

公钥和私钥的用途。

### 三、创建或引入一个项目

点击主界面tab切换后面的+号，打开如下面板

![TPL_中台产品部 > Git图像化工具使用 > gitclone.png](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639709643893sourcetree3.png)

- 1、local 可以展示当前我们本地打开的项目有哪些？
- 2、remote，sourceTree会记录你的远程仓库地址，你可以引入远程仓库内任意的项目
- 3、clone，相当于git clone,只要有项目地址就可以将项目clone到本地
- 4、add 如果你本地有个git项目，那么你可以直接引入
- 5、create 选择一个不是仓库的文件夹，创建一个git仓库，相当于git init
- 这样我们就可以通过sourceTree使用git命令操作项目了。

### 工作示例

![TPL_中台产品部 > Git图像化工具使用 > sourceTree.png](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639709648769sourcetree4.png)

sourceTree 整个界面分成了一个区域，这里我们重点介绍下这7个区域，也是我们常用的地方

> 第一个区域，主要是git的常用操作，包括
>
> - 提交（git add）、
> - 拉取（git pull）、
> - 推送（git push）、
> - 获取（git fetch）、
> - 分支（git branch、git checkout、git push -d branch）
> - 合并（git merge）
> - 贮藏（git stash）、
> - 丢弃（git reset —HEAD）
> - 标签（git tag）
>
> 第二个区域，主要是一些功能性操作，方便一键操作
>
> - git工作流，主要是自动创建不同的分支流程，帮助多人合作开发管理分支，类似我们章节分支管理中的内容
> - 远端：点击展示当前项目远程仓库信息，提供git remote -v 和修改删除的功能
> - 命令行模式，window下快速打开bash命令行
> - 资源管理器，一键打开项目所在的文件夹目录
> - 设置远程仓库配置和git配置信息设置
>
> 第三个工作区

- WORKSPACE 工作区信息
- 文件状态，主要展示当前项目你追踪的修改、删除的文件或未追踪，新建的文件 git status
- 点击文件状态后，区域5变成了下面这个样子，将git的仓库分成了工作区（未暂存区，暂存区、git diff区）
- History, git log, 暂时文件提交的历史和说明，分支合并的路径等信息 git grash
- Search 提供了对git log的一个筛选和查找

> 第四个工作区

- 分支 使用的命令包括 git branch -a
- 很直观的可以看到本地当前创建的分支
- 标签：展示所有的标签内容
- 远程仓库所有的分支
- 贮藏区：相当于git stash，保存在本地的临时代码

> 第五一个区域

主要配置各个tab切换展示不同的内容，筛选，日志数据，文件状态分区展示，主要要的是清晰的展示了各个分支之间的关系和来龙去脉

> 第六工作区

上面主要展示了，git logs commit-id和两个版本之间的修改内容

> 第七个工作区

主要展示某个commit-id修改内容的差异
右边还可以通过回滚区块，来改动小地方的恢复，非长方便