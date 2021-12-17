# config

想要使用git首先需要安装，安装完毕后需要配置git信息

我们通过git config命令设置git的相关信息，这样每次提交就可以带上自己的信息

但是我们给项目绑定远程仓库后，每次提交都要输入密码，那么多项目，密码不好记还不方便。下面我们介绍下通过publickey 来绑定仓库，每次直接提交。

给项目配置publickey有两种方法，一种是直接给用户下绑定，那么拥有publickey对应私钥的主机可以访问该用户下所有的项目

单独给某个项目配置key,这样当前用户就只能访问这个项目。

1、安装git

2、git config

3、生成sshkey

4、登录github，新建项目，给项目下添加key

5、给用户添加key，可以访问所有的项目


首先需要在使用的本机上生成ssh-key，然后项目public-key加入到gitlab或githuab等远程仓库，这样我们就可以直接拉取仓库的内容。（当然，如果项目有权限的话还要添加访问，修改等权限，一般在管理员的setting选项中设置）


## 生成ssh
1、先执行以下语句来判断是否已经存在本地公钥： 
cat ~/.ssh/id_rsa.pub 
2、如果你看到一长串以 ssh-rsa或 ssh-dsa开头的字符串, 你可以跳过 ssh-keygen的步骤。否则 
你可以按如下命令来生成ssh key： 
ssh-keygen -t rsa -C “835570372@qq.com” 
3、这个指令会要求你提供一个位置和文件名去存放键值对和密码，你可以点击Enter键去使用默认值。 
用以下命令获取你生成的公钥： 
cat ~/.ssh/id_rsa.pub

## git操作
> 查看

git config user.name

git config user.email

> 设置

git config --global user.name "pengfei.lv"

git config --global user.email "pengfei.lv@ucloud.cn"

