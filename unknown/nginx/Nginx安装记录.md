# nginx安装记录

nginx 是一款高并发的轻量级web代理服务器，它可以运行在Linux,Windows,Mac OS等操作系统上。

这里汇总下三种安装方式

- 命令行安装
- 源码安装
- docker安装

## 一、命令行安装

### 1、安装在linux

（1）CentOS 安装(yum)

```shell
yum install nginx -y  // 安装nginx
yum remove nginx   // 卸载nginx
```

设置nginx开机自启动

```shell
systemctl  enable  nginx.service
systemctl  restart  nginx.service
```

设置开启防火墙

```
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --reload
注：centOS 一般默认都是安装了yum，运行yum –version 查看nginx,如果没有安装，需要去安装一下
```

（2）utuntu 安装（apt-get）

```
sudo apt-get update
sudo apt-get install nginx
```

### 2、安装在window

（1）下载安装包 http://nginx.org/download/（zip安装包为window安装包）

（2）解压，找到exe文件nginx-win

```
conf   nginx 配置文件

html  nginx默认访问的html文件

logs  日志文件（错误日志，访问日志）
```

（3）cmd nginx当前所在的目录

### 3、相关命令

```shell
cd C:\Users\user\Downloads\nginx-1.16.1
# 查看版本
nginx.exe -v
# 启动
start nginx 或  nginx.exe
# 打开浏览器，输入http://localhost
# 停止
nginx.exe -s stop  或  nginx.exe -s quit
# 重启
nginx.exe -s reload
# 重新打开日志文件
nginx.exe -s reopen
注：nginx在win性能较差，所以win仅仅用来体验学习就好。
```

## 二、nginx源码编译安装

> nginx有很多好用的第三方插件，如果需要安装的话，nginx安装

nginx源码编译，通常会出现很多小问题，所以对nginx要求不高，配置比较简单，可以选择工具安装.

这里由centos为例用源码进行安装

nginx是由一个个模块组成的，有一些自带的模块和大量的第三方模块，也可以自行开发第三方模块。

### 1、前置工作

安装一些nginx依赖的库

```
yum install -y gcc //安装GCC

...安装过程省略
yum install -y gcc-c++ //安装C++库用来编译c++

...安装过程省略
yum install -y pcre pcre-devel //安装pcre库

...安装过程省略
yum install -y zlib zlib-devel //安装lib库

...安装过程省略
yum install -y openssl openssl-devel
...安装过程省略
```

### 2、下载源码

首先创建/var/nginx目录，可以自定义
`cd /var/nginx`

```
源码download  #http://nginx.org/download 
wget http://nginx.org/download/nginx-1.9.10.tar.gz
nginx源码目录

![nginx源码目录](https://gitee.com/pengfeilv/picture-manage/raw/master/blog/nignx_menu.png)

auto：一些自动编译的脚本，配合configure使用
CHANGES：版本变化
CHANGES.ru：俄罗斯语言的版本变化
conf：nginx配置文件
configure：nginx安装编译文件
contrib 存放其他机构或组织贡献的代码
html：nginx配置root默认模块
LICENSE：许可文件
man：通过man ./nginx.8查看，nginx的帮助手册
README：说明文档地址
src：nginx主要的源码分布
nginx安装指令
当前目录/var/nginx/nginx-1.9.10

./configure --help   查看安装的一些参数
–prefix=PATH 设置nginx的安装路径
–sbin-path=PATH 设置环境变量的路径
–with-http_ssl_module 等是可以安装的模块
–without-http_charset_module 等是nginx自带的模块，加上该参数可以卸载
–add-module=PATH 添加第三方模块
```

### 3、安装步骤

当前目录/var/nginx/nginx-1.9.10

```shell
# 执行命令，配置不同的参数，具体参数见上面安装指令
./configure --prefix=/usr/local/nginx  

# 编译
make && make install

# 安装完成后(nginx在/usr/local/nginx中)
whereis nginx

# nginx可执行文件在/usr/local/nginx/sbin/nginx  将nginx添加环境变量(软连接方式)
ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx

# 启动
nginx
```

### 4、常见问题

(1) 安装报错

```shell
checking for struct tm.tm_gmtoff ... found
checking for struct dirent.d_namlen ... not found
checking for struct dirent.d_type ... found
checking for sysconf(_SC_NPROCESSORS_ONLN) ... found
checking for openat(), fstatat() ... found
checking for getaddrinfo() ... found
checking for PCRE library ... not found
checking for PCRE library in /usr/local/ ... not found
checking for PCRE library in /usr/include/pcre/ ... not found
checking for PCRE library in /usr/pkg/ ... not found
checking for PCRE library in /opt/local/ ... not found
./configure: error: the HTTP rewrite module requires the PCRE library.
You can either disable the module by using --without-http_rewrite_module
option, or install the PCRE library into the system, or build the PCRE library
statically from the source with nginx by using --with-pcre=<path> option.
```

解决办法

```
yum install pcre-devel

./libtool: line 990: g++: command not found
```

解决办法

```
yum -y install gcc+ gcc-c++
```

(2) nginx启动报错

![启动报错](https://gitee.com/pengfeilv/picture-manage/raw/master/blog/nginx_err.png)

```
Starting nginx (via systemctl): Job for nginx.service failed because a configured resource limit was exceeded. See “systemctl status nginx.service” and “journalctl -xe” for details.

Failed to start SYSV: Nginx is an HTTP(S) server, HTTP(S) reverse proxy and IMAP/POP3 proxy server.
```

解决办法：

`netstat -ntlp` 查看在运行的nginx进程

`kill -9 pid` 杀死正在运行的nginx进程

## 三、Docker安装nginx

工作中，我们常常使用docker部署服务，这样的话，如果将nginx打包成镜像，改动起来不太方便，日志查询也很麻烦，所以我们可以将nginx和日志挂载在容器外。

服务器选择ucloud，购买请点击

### 1、基础

相关技术：docker、nginx

docker仓库：`https://hub.docker.com/_/nginx`

拉取不同版本的镜像：`https://hub.docker.com/_/nginx?tab=tags`

### 2、拉取镜像并启动

启动一个nginx容器
注意：使用端口一定要保证端口的防火墙打开

`docker run --name some-nginx -d -p 8002:80 nginx`
![docker启动nginx容器](https://gitee.com/pengfeilv/picture-manage/raw/master/blog/nginx_web.png)

我们可以看到一行命令就可以启动nginx

接着我们执行命令docker exec -it some-nginx bash 就可以进入容器操作nginx的配置，从而使用它。

### 3、挂载nginx配置和日志

上面使用docker修改配置和查看日志不方便，所以我们部署一个docker，将nginx的配置和日志放在宿主机上

#### 1、在宿主机 /data/web 新建目录

```
web
├── logs
│ ├── access.log
│ └── error.log
├── nginx
│ └── nginx.conf
└── testWeb
└── index.html
```

nginx.conf

```nginx
server {
   listen  80;
   server_name  localhost;
   root    /usr/share/nginx/html;

   location / {
     index  index.html;
   }
}
```

index.html

this is testWeb index.html;

#### 2、启动docker容器

```
docker run --name test-nginx -d -p 8003:80 -v /data/web/logs:/var/log/nginx -v /data/web/nginx:/etc/nginx/conf.d -v /data/web/testWeb:/usr/share/nginx/html nginx
访问：curl http://localhost:8003，返回index.html内容
```

#### 3、修改index.html文件

this is testWeb index.html; this file is change;
访问：curl [http://localhost:8003](http://localhost:8003/) 返回修改后的内容，查看access.log 发现日志正常打印。

![日志文件](https://gitee.com/pengfeilv/picture-manage/raw/master/blog/nginx_text.png)