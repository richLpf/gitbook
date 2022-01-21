## 一、问题背景

原因：微信jssdk，https服务通过TLSv1.3加密请求，本地nginx仅支持1.2，导致请求失败

<img src="https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1642757761729sdk.png" alt="抓包工具" style="zoom:50%;" />

**nginx配置**

![nginx配置](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1642757888613nginx.png)

因此，需要让openssl支持TLSv1.3版本，修改这一行配置

```nginx
ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
```

执行命令`nginx -t`检测nginx配置发现报错

<img src="https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1642758161412https-err.png" alt="版本" style="zoom:50%;" />

执行nginx -V，发现nginx版本和openssl版本分别为：1.10.1和1.0.1e，不支持TLSv1.3

Centos版本是：`Centos release 6.3`

<img src="https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1642758312033nginx-version.png" alt="nginx版本" style="zoom:50%" />

因此，需要升级openssl。

## 二、升级openssl 版本

[openssl下载地址](https://www.openssl.org/source/)

```shell
cd /usr/local
# download
wget https://www.openssl.org/source/openssl-1.1.1m.tar.gz
# tar,这里下载的是openssl-1.1.1m.tar.gz，版本，根据自己实际情况替换
tar xf openssl-1.1.1m.tar.gz
cd openssl-1.1.1m
# 执行编译
./config shared zlib
make
make install
# 版本替换，这里根据系统执行命令的环境变量地址替换
rm -rf /usr/bin/openssl
rm -rf /usr/include/openssl
# 将新的openssl的地址/user/local/bin/openssl通过软链接添加环境变量中
ln -s /usr/local/bin/openssl /usr/bin/openssl
ln -s /usr/local/include/openssl /usr/include/openssl
# >>追加内容
echo "/usr/local/lib64/" >> /etc/ld.so.conf
ldconfig -v
# 如果没有报错，那么就安装完成了,执行命令查看版本
openssl version
# 打印出：OpenSSL 1.1.1m  14 Dec 2021
```

升级openssl完毕后，执行`nginx -V`发现nginx关联的openssl并没有变化，因为nginx需要重新编译。

## 三、升级nginx版本

nginx一般有两种安装方式：yum等工具安装和源码安装，详细内容可以看之前的文章[nginx安装]()

这里因为要集成多种插件，选择nginx安装。

**安装之前，请先备份nginx配置，防止出现问题，回不来了**

```shell
cp -r /etc/nginx /etc/nginx_backup
```

安装包放在`/usr/local`目录下

```nginx
# 将原来的nginx服务停下来
systemctl nginx stop
## 或 service stop nginx

# 安装目录
cd /usr/local/
# 顺便升级下nginx版本
wget https://nginx.org/download/nginx-1.16.0.tar.gz
tar -zxvf nginx-1.16.0.tar.gz 
cd nginx-1.16.0
# 配置nginx，并选择安装插件
./configure --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_stub_status_module --with-http_auth_request_module --with-http_xslt_module=dynamic --with-http_image_filter_module=dynamic --with-http_geoip_module=dynamic --with-http_perl_module=dynamic --with-threads --with-stream --with-stream_ssl_module --with-http_slice_module --with-mail --with-mail_ssl_module --with-file-aio --with-http_v2_module --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector --param=ssp-buffer-size=4 -m64 -mtune=generic' --with-openssl=/usr/local/openssl-1.1.1m
make
# make重新编译即可，不用重新make install
# 安装过程中，很容易遇到报错，大多是包的缺失和路径的问题。

# 启动nginx
systemctl nginx start
```

我们可以看到最后，添加的插件`--with-openssl=/usr/local/openssl-1.1.1m`, 这个路径**就是安装包解压后的路径**

安装成功后，执行`nginx -V`，发现版本已经变化了

![升级后的nginx版本](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1642761527029nginx-version-1.16.png)

## 四、遇到的问题

### 1、问题一：路径不正确

```nginx
cc: /usr/local/openssl-1.1.1m/lib/libcrypto.a: No such file or directory
make[1]: *** [objs/nginx] Error 1
make[1]: Leaving directory `/usr/local/nginx-1.16.0'
make: *** [build] Error 2
```

很明显，是因为找不到了`/openssl-1.1.1m/lib/libcrypto.a`, 因此查看路径

**查看/usr/local/openssl-1.1.1m中libcrypto.a的路径**

![openssl目录](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1642760854737openssl.png)

发现，`libcrypto.a`并不是在/lib目录下，所以需要修改nginx配置文件

```nginx
# 打开nginx配置文件
/usr/local/nginx-1.16.0/auto/lib/openssl/conf

# 修改路径
CORE_INCS="$CORE_INCS $OPENSSL/.openssl/include"
CORE_DEPS="$CORE_DEPS $OPENSSL/include/openssl/ssl.h"
CORE_LIBS="$CORE_LIBS $OPENSSL/libssl.a"
CORE_LIBS="$CORE_LIBS $OPENSSL/libcrypto.a"
CORE_LIBS="$CORE_LIBS $NGX_LIBDL"
```

**修改完毕后，需要从`../configure --prefix...`执行**



### 2、问题二：安装包缺失

```shell
checking for perl
 + perl version: v5.10.1 (*) built for x86_64-linux-thread-multi
Can't locate ExtUtils/Embed.pm in @INC (@INC contains: /usr/local/lib64/perl5 /usr/local/share/perl5 /usr/lib64/perl5/vendor_perl /usr/share/perl5/vendor_perl /usr/lib64/perl5 /usr/share/perl5 .).
BEGIN failed--compilation aborted.

./configure: error: perl module ExtUtils::Embed is required
```

解决办法：

```shell
yum -y install perl-devel perl-ExtUtils-Embed
```

## 五、修改Nginx配置

```nginx
server {
	## 有变化
  listen                   443 ssl;
  server_name              uhappy.test.com;

	## 下面这个废除了
  #ssl                      on;
  ssl_certificate          /etc/nginx/certs/_.test.com.crt;
  ssl_certificate_key      /etc/nginx/certs/_.test.com.key;

  ssl_session_timeout  5m;

  ssl_ciphers HIGH:!aNULL:!MD5;
  
  ## 支持TLSv1.3
  ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
  ssl_prefer_server_ciphers   on;
  location / {
    root                    /data/front/build;
    try_files               $uri $uri/ @router;
  }
  location @router {
    rewrite ^.*$ /index.html last;
  }            
}
```

## 六、参考资料

- [Nginx安装过程中常见问题](https://cloud.tencent.com/developer/article/1401078)
- [解决Nginx添加openssl模块编译时报错问题](https://blog.51cto.com/983836259/1835809)