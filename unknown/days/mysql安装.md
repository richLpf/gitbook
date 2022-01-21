# mysql安装踩坑

使用docker安装`Mariadb`过程中出现了很多问题，这里汇总下

启动环境 centos8

## 一、首先使用`docker-compose`启动

```yaml
version: '3'

networks:
  app-web:
   driver: bridge

services:
  mysql:
    image: mariadb
    ports:
     - 3306:3306
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    networks:
     - app-web
    environment:
     - TZ=Asia/Shanghai
     - MYSQL_USER=root
     - MYSQL_ROOT_PASSWORD=123456
    privileged: true
    volumes:
     - ./mysql/data:/var/lib/mysql
     - ./mysql/my.cnf:/etc/mysql/my.cnf
     - /etc/localtime:/etc/localtime
```

发现报错，容器内文件没有访问权限，用镜像启动的数据库没有root用户。

```shell
2021-10-10 12:25:27+08:00 [Note] [Entrypoint]: Entrypoint script for MariaDB Server 1:10.6.4+maria~focal started.
2021-10-10 12:25:27+08:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2021-10-10 12:25:27+08:00 [Note] [Entrypoint]: Entrypoint script for MariaDB Server 1:10.6.4+maria~focal started.
2021-10-10 12:25:28+08:00 [Note] [Entrypoint]: Initializing database files
chown: changing ownership of '/var/lib/mysql/': Operation not permitted
Cannot change ownership of the database directories to the 'root'
user.  Check that you have the necessary permissions and try again.
```

- 1、docker-compose.yml 增加配置`privileged:true`，还是会有同样的问题
- 2、查看资料发现下面两种解决方法



### 1、方法一

```
$ docker run -d -e MYSQL_ROOT_PASSWORD=admin -v /home/epccweb/test:/var/lib/mysql --user 1000:50 mysql:5.7
$ # or whatever user and group id that the container sees on the mounted folder:
$ docker run -it --rm -v /home/epccweb/test:/var/lib/mysql mysql:5.7 ls -aln /var/lib/mysql
```

由于要先启动容器，在修改，这里已启动就挂，所以没发使用。

### 2、方法二

https://stackoverflow.com/questions/24288616/permission-denied-on-accessing-host-directory-in-docker
进入主机的`vi /etc/selinux/config`尝试禁用主机的`SELINUX=disabled`,发现本来就是禁止的，所以暂时放弃该方法启动数据库



## 二、使用docker命令行启动数据库

```
docker run -d --restart=always -p 3306:3306  -e MYSQL_USER=root -e TZ=Asia/Shanghai --privileged=true  -e MYSQL_ROOT_PASSWORD='123456' -v /data/mysql/data:/var/lib/mysql -v /data/mysql/my.cnf:/etc/mysql/my.cnf -v /etc/localtime:/etc/locatime mariadb
```

其中`mysql/my.cnf`文件内容为：

```
skip-name-resolve
user=root
character-set-server=utf8
default_authentication_plugin=mysql_native_password
sql_mode=ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
lower_case_table_names=1 #忽略表名大小写
#skip-grant-tables

[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
```

成功启动docker镜像
![容器](https://gitee.com/pengfeilv/picture-manage/raw/master/blog/docker.png)

尝试进入数据库，发现报错，但密码不生效

```
1045 - Access denied for user 'root'@'localhost' (using passwor...)
```

在`my.cnf`中增加`skip-grant-tables`, 跳过密码登陆，然后执行下面命令修改用户密码

```
use mysql;
update user set password=password("123456") where user="root";
```

报错信息：

```
ERROR 1348 (HY000): Column 'Password' is not updatable
// 继续执行报错
MariaDB [mysql]> ALTER USER 'root'@'localhost' identified by '123456';
ERROR 1290 (HY000): The MariaDB server is running with the --skip-grant-tables option so it cannot execute this statement
// 重启下：flush privileges;
// 再次执行
MariaDB [mysql]> set password for 'root'@'localhost' = '123456';
ERROR 1372 (HY000): Password hash should be a 41-digit hexadecimal number
// 对密码进行转化
MariaDB [mysql]> select password(123456)
    -> ;
+-------------------------------------------+
| password(123456)                          |
+-------------------------------------------+
| *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 |
+-------------------------------------------+
// reset password
MariaDB [mysql]> set password for 'root'@'localhost' = '*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9';
Query OK, 0 rows affected (0.002 sec)
```

然后删除`my.cnf`中的`skip-grant-tables`,重启容器，使用密码登陆，发现成功成功

## 三、参考记录

- https://stackoverflow.com/questions/59941858/how-to-set-root-password-in-mariadb-10-4-on-macos
- https://www.cnblogs.com/renpingsheng/p/9298134.html
- https://www.cnblogs.com/YDDMAX/p/6184100.html
- https://blog.csdn.net/qq_36735409/article/details/78032144
- https://www.cnblogs.com/nhdlb/p/13723717.html