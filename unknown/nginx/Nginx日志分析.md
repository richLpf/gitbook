# Goaccess做前端nginx日志分析

goaccess 是一款强大的web日志分析工具，可以定义不同类型的日志格式,支持生成html页面，json等格式文档

## 一、安装

https://goaccess.io/download
这里是linux安装方法，其他系统可以看下官网

```
$ wget https://tar.goaccess.io/goaccess-1.4.tar.gz
$ tar -xzvf goaccess-1.4.tar.gz
$ cd goaccess-1.4 /
$ ./configure --enable-utf8 --enable-geoip =旧版
```

## 二、排错处理

参考资料：https://www.iteye.com/blog/z63as-2433509

```
如果报错：
configure: error: *** Missing development libraries for ncursesw
执行下面两个命令即可
yum install ncurses-libs
yum install ncurses-devel
如果报错：
configure: error: *** Missing development libraries for ncursesw
执行下面两个命令即可
yum install ncurses-libs
yum install ncurses-devel
```

## 三、实时生成日志

goaccess 有很多命令行，可以在linux实时查看访问情况，也可以生成html查看。
不过文档中将可以使用 `--real-time-html-` 就可以实现实时查看，不过我这边刷新没有效果，所以使用crontab 定时生成report.html 实现每次打开都是最新的数据
首先：
nginx日志 `access.log` 放在 `/var/log/nginx` 下, 生成的 `report.html` 也放在改目录下面
`goaccess /var/log/nginx/access.log -o /var/log/nginx/report.html --time-format='%H:%M:%S' --date-format='%d/%b/%Y' --log-format=COMBINED`
运行指令，可以发现/var/log/nginx下生成了report.html文件，我们用nginx部署下静态文件

```nginx
server {
  listen 8000;
  server_name _;
  location /report {
  	alias /var/log/nginx/report.html; 
  }
}
```

在浏览器访问 http://host:8000/report.html 就可以看到如下的页面

最后增加定时任务
`crontab -e`
`*/5 * * * * goaccess /var/log/nginx/access.log -o /var/log/nginx/report.html --time-format='%H:%M:%S' --date-format='%d/%b/%Y' --log-format=COMBINED`