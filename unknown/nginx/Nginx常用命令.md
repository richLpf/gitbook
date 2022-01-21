# nginx常用命令汇总

nginx 主要通过发射信号来控制进程，了解这些命令可以帮助我们更好的使用nginx

下面是nginx的命令思维导图

![nginx命令行](https://gitee.com/pengfeilv/picture-manage/raw/master/blog/nginx_xmind.png)

## 一、nginx命令行

nginx 使用信号控制进程，通过nginx命令我们可以实现各种操作

- nginx -s stop 直接停止nginx
- nginx -s quit 优雅停止nginx(nginx会在所有请求执行完毕，依次关闭进程，最后退出)
- nginx -s reload 重新载入配置，平滑重启nginx, 新的请求会走新的配置，老的请求处理完毕后关闭nginx进程。
- nginx -s reopen 重新打开日志文件 适用于切割nginx日志操作
- nginx -t 检查配置文件语法是否正确，-T 同时打印出配置文件，后面跟文件路径，可以检查某个文件
- nginx -h 显示nginx的可以执行的命令
- nginx -v 查看nginx版本 -V 通知显示nginx的安装信息和安装的模块。
- nginx -c <配置文件目录> 来启动非默认的配置
- nginx -p <目录> 来指定nginx的安装目录
- nginx -g 来临时指定一些全局配置项
- nginx -g “pid /var/nginx/test.pid;” 意味着把pid写到另一个文件中，-g指定的不能与默认冲突，另外以-g启动的ngix在停止事也需要加上-g

- nginx -g “pid /var/nginx/test.pid;” -s stop. 如果不加-g 就找不到pid文件了

## 二、其他命令使用

### 1、使用系统命令来启动nginx

```
systemctl start nginx  或  service nginx start 

systemctl status nginx 或  service nginx status

systemctl stop nginx 或 service nginx stop
```

### 2、使用kill 启动nginx

- kill -HUP pid pid是进程标识，使用该命令发起一个挂起信号，服务器进程会进行复位操作，重新加载配置文件。
- TERM, INT 快递关闭
- QUIT 从容关闭
- HUP 平滑重启，重新加载配置文件
- USER1 重新打开日志文件
- USER2 平滑升级可执行程序
- WINCH 从容关闭工作进程

## 三、二者区别

- 1、systemctl 或 service 启动nginx, 会关掉nginx并重新启动nginx进程id
- 2、nginx -s reload 或 kill -HUP pid 必须在nginx 启动后使用
- 3、nginx -s reload 和 kill -HUP pid 会保留旧的请求，直到连接全部结束后退出，新的请求通过新的配置启动进程。