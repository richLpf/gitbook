# nginx日志

## 一、access_log

> access_log, nginx可以记录访问日志，通过日志可以查看用户访问的哪些页面比较平凡，哪些服务比较高频的调用，下面是nginx日志的语法

Syntax: | access_log path [format [buffer=size] [gzip[=level]] [flush=time] [if=condition]];``access_log off;
Default:| access_log logs/access.log combined;
Context:| http, server, location, if in location, limit_except

`nginx.conf`,`access_log`具体用法：

```
http {
	include           mime.types;
	default_type      application/octet-stream;
	log_format main   '$host  $remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for" "$request_uri"';
  access_log        logs/access.log   main;
  sendfile          on;
  server {
  	listen          80;
  	server_name     localhost;
  	location  / {
		  index index.html;
	  }
}
```

## 二、log_format

log_format 指令指定nginx日志的输出格式， 比如上面nginx.conf 文件，log_format指令声明了main格式的(默认格式combined)，后面定义了具体的格式，当我们请求nginx，会输出如下的日志

```
113.31.124.1  115.172.233.1 - - [25/Apr/2020:08:27:59 +0800] "GET / HTTP/1.1" 304 0 "-" Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.04044.122 Safari/537.36" "-" "/index.html"
```

对应下面的变量, 没有用 `-` 表示

```
'$host  $remote_addr - $remote_user [$time_local] "$request" '
'$status $body_bytes_sent "$http_referer" '
'"$http_user_agent" "$http_x_forwarded_for" "$request_uri"';
```

## 三、对应变量的含义

- $host 访问的域名
- $remote_addr 访问的客户端ip
- $remote_user 用户名
- $time_local 请求时间和时区
- $request 请求的http信息
- $status 返回状态码
- $body_bytes_send 服务端返回给客户端的body信息
- $http-referer 请求访问来源，根据该参数设置反盗链
- $http_user_agent 客户端的访问信息
- $http_x_forwarded_for 记录请求过程中的ip，再经过反向代理时，可以追踪访问的ip
- $request_uri 记录请求的路由

## 四、日志缓存和压缩

access_log main buffer=256k gzip flush=5;
access_log 后面可以增加参数

buffer 代表在服务器内存中开启一块内存，大小这里设置为256k, nginx会先缓存日志到内存中，日志大小等256k，会写入磁盘，用来减少服务器的写入操作，提高服务器的性能，但日志将不饿能实时的看到。

gzip 代表输入的日志压缩等级，1-9代表压缩等级越来越高，写入速度越来越慢。默认值为1

flush 代表定时刷新内存中缓存的日志，写到磁盘中（不管buffer缓存的日志是否达到内存设置的上限），单位是秒。

## 五、error_log

语法：error_log path level;

作用域： main http server location

示例：error_log /logs/error_log.log error;

错误日志在不同作用域中的默认级别是不同的，main: error; http: crit; server: crit;

error_log 的错误级别有： debug|info|notice|warn|error|crit|alert|emerg, 从左到右级别越高，记录的错误越严重。

该指令在一个模块中同时只能出现一次，多次报错，在不同模块中，子模块会覆盖父模块的指令

error_log /var/log/nginx/error_log crit;

## 六、日志分割

`split_log.sh` 脚本

```
#/bin/bash
base_path='/usr/local/nginx/logs'
echo $base_path

day=$(date +%Y%m%d)
echo $day
mv $base_path/access.log $base_path/access_log_$day.log
PID='/usr/local/nginx/logs/nginx.pid'
kill -USR1 `cat ${PID}`
```

## 七、执行

给执行权限

```
chmod +x split_log.sh
```

加入定时脚本，一天切割一次

```
crontab -e
00 03 * * * /usr/local/nginx/logs/split_log.sh
```

## 八、日志切割工具

- [Logrotate](