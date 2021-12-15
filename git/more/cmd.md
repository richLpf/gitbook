# 常用操作

> 远程仓库

```
# 查询远程仓库
git remote -v

# 删除远程仓库
git remote rm origin

# 添加远程仓库
git remote add origin git@github.com:richLpf/antd-template-demo.git

# 再添加一个远程仓库

git remote set-url --add origin htts://url
```


> 迁移仓库

```
cd existing_repo
git remote rename origin old-origin
git remote add origin git@git.utets.comm:test.v/test.git
git push -u origin --all
git push -u origin --tags

```


## git多用户切换

不同的项目需要设置不同的用户信息

只需要修改本地项目下`vim .git/config`用户信息即可


```
user@FVFFG2HUQ6LR .git % cat config 
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
[remote "origin"]
        url = git@github.com:test/test.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
        remote = origin
        merge = refs/heads/master
[user]
        name = tet
        email = test@163.com
```


