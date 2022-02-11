## 前言

前端部署服务器，需要经过打包、迁移、部署、回退操作，如果每次都手动处理，那么容易出错还浪费时间，自动化部署又比较复杂，那么选择shell脚本做自动化，也是一个不错的选择，具体实现如下：

## 项目环境

- 执行自动化构建的前端项目`myweb`
- 项目通过命令构建测试环境文件或生产环境文件
- linux服务器，nginx部署项

## 实现原理

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c22733e3f80f4d46bc72879beefc2355~tplv-k3u1fbpfcp-watermark.image)


### 一、构建一个前端项目

通过cra构建前端项目，修改配置文件，通过传递配置参数，配置测试环境和生产环境

1、cra 构建项目，运行项目

```
yarn create react-app myweb

cd myweb

yarn install
```

2、修改项目文件`package.json`

安装依赖`corss-env`，执行命令`yarn add -D cross-env`，该包用来修改环境变量

```json
"scripts": {
    "start": "react-scripts start",
    "build:pre": "cross-env REACT_APP_BUILD_ENV=development react-scripts build",
    "build:prod": "cross-env REACT_APP_BUILD_ENV=production react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
```

在项目中我们获取`process.env.REACT_APP_BUILD_ENV`就可以获取当前项目的打包环境

执行命令`yarn build:pre`，打包测试环境
执行命令`yarn build:prod`，打包生产环境

### 二、打包和迁移的shell脚本

这一步将打包的文件通过shell脚本迁移，通过传参判断不同的打包环境。

在根目录下新建`publish.sh`

```sh
#!/bin/bash
function build(){
    echo $1
    # 执行打包命令
    yarn build:$1
    # 压缩打包后的文件
    zip -r build.zip ./build/*
    # 迁移压缩文件到服务器对应目录下
    scp ./build.zip root@117.50.4.30:/data/myweb/build
    # 删除上传后的压缩文件
    rm -rf build.zip
}
if [ $1 == "pre" ]; then
build $1
elif [ $1 == "prod" ]; then
build $1
else
echo "your params is not pre or prod"
fi
```

修改`package.json`, 增加发布命令

```
"scripts": {
    "publish:pre": "sh publish.sh pre",
    "publish:prod": "sh publish.sh prod"
}
```

### 三、执行脚本，部署上线

1、在服务器部署前端项目

登陆服务器，在服务器上新建`/data/myweb`，配置nginx文件

```nginx
server {
    listen       80 default_server;
    listen       [::]:80 default_server;
    server_name  _;
    
    # 项目部署的路径
    root         /data/myweb/build;
    location / {
        index index.html;
    }
}
```

2、执行脚本发布

执行第三步配置的发布脚本`yarn publis:pre`，
输入服务器密码，将迁移到部署服务器的`/data/myweb`下，
然后执行`unzip build.zip`，将部署文件解析到`/build`下，
在浏览器打开`http://117.50.4.30`，即可看到部署的文件

**注意：确保80端口开放**

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3e4e83f3dfa0415d8d21947f58830d47~tplv-k3u1fbpfcp-watermark.image)


### 四、脚本发布和备份

第四步，可以看到上传到服务器后，还需要执行`unzip build.zip`来部署脚本，也是很不方便，继续改进shell,
在`/data/myweb`下新建`online_and_backup.sh`,

1、增加脚本备份打包文件`online_and_backup.sh`

```sh
#!/bin/bash
# 首先备份当前版本v1.0.0
zip -r build.zip.old ./build/*
cp ./build.zip.old /data/myweb/backup/build.zip.old
# 解压最新的版本v1.0.1
unzip -o build.zip
# 获取当前的时间，拼接下
current_time=$(date "+%Y%m%d%H")
cd /data/myweb/backup
mv build.zip.old build.zip."$current_time"
```

build.zip.old 为备份的文件的打包文件
`/data/myweb/backup` 下的文件为备份的不同版本，可以增加定时清理脚本，只保存一周的数据，防止出现问题可以及时回退到某天的版本或者某个版本


### 五、脚本回退

在`/data/myweb`下新建`back.sh`，主要作用，发布后出现问题，将之前备份的文件重新部署

1、增加回退脚本`back.sh`

```sh
mv build.zip build.zip.err
mv build.zip.old build.zip
unzip -o build.zip
```

### 六、多人开发，方便合作

#### 1、项目通过跳板机发布

该项目仅仅展示了直接上传上生产环境服务器的脚本，一般发布如果有跳板机的需要跳板机过渡，增加一个迁移脚本即可

#### 2、脚本文件保存

在项目根目录下新建文件夹`scripts`,将我们写好的发布脚本`publish.sh`,备份脚本`backup.sh`,回退脚本`back.sh`放在下面。上传git，这样其他人可以使用或者项目迁移也可以使用。

#### 3、多人开发，上传不同的跳板机

由于多人开发需要，可能需要将文件上传到自己的跳板机，所以在`.gitignore`文件文件中增加，增加一个人开发，就可以复制`publish:sh`到自己的项目根目录，修改自己的配置，可以做到多人不相互干扰。

`.gitignore`
```
build.zip
publish.sh
```

#### 4、增加功能
- 通过shell脚本修改版本，做版本管理
- 在提交钱进行代码格式化整理

#### 5、示例项目

```
https://github.com/richLpf/my-web-shell
```