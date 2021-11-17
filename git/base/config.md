# 安装配置

git的安装很简单：

分为源码安装和安装包安装，我们选择安装包安装就可以了

## window安装，只需要到官网下载安装包就可以了

下载地址：https://git-scm.com/download/win

安装完成后，鼠标右键会发现增加了git bash/git gui 两个命令

我们点击git bash命令，会出现一个命令提示符，执行`git --version`就可以看到安装的git版本

## Git的环境变量配置

首先查看Git的所有配置和所在文件

`git config --list --show-origin`

Git可以读取的变量信息包括三个地方

1、所在系统的
linux: /etc/gitconfig
window: $HOME  一般为C:\Users\$USER\.gitconfig

2、当前用户的
linux: ~/.gitconfig 或 ~/.config/git/config
window：

3、git仓库目录中 
linux/window:  .git/config

git读取的配置会相互覆盖，3的权重最高，2的权重次之，1的权重最低

## Git的用户信息配置

第一次运行Git前需要配置用户信息

username 可以自定义用户名，之后每次提交版本将会显示改名称
email 自己的用户邮箱，之后提交会显示出来

```
git config --global user.name <username>

git config --global user.email <email>
```

读取配置信息

读取所有的用户信息

```
git config --list

```

读取用户名
```
git config user.name
```

读取用户邮箱
```
git config user.email
```


