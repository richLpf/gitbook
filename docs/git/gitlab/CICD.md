# gitlabCICD

我们新建项目cicdTest项目，

```
mkdir cicdTest

touch index.html

```

`index.html`文件

```
<html>
<head>
  <title>CICD</title>
</head>
<body>
  <h1>Hello CICD!</h1>
</body>
</html>
```

首先要触发CI, 我们要在项目的根目录下新建文件`.gitlab-ci.yml`

此时cicdTest目录结构为

```
- index.html
- .gitlab-ci.yml
```

下面我们介绍下.gitlab-ci.yml 具体的配置文件

`.gitlab-ci.yml`

首先我们选择要执行cicd使用的基础镜像

```
# 运行该文件需要使用的镜像和指定的版本
image: nginx

```

接着我们写cicd执行的任务,.gitlab-ci.yml 

一、有很多预定义的环境变量，可以使用，具体用法如下，$CI_JOB_STAGE打印出来test

```
image: nginx:1.18.0
test_variable:
	stage: test
	script:
		- echo $CI_JOB_STAGE
```

二、.gitlab-ci.yml 还有可以自定义变量

```
variables:
	TEST: "HELLO WORLD"

image: nginx:1.18.0
test_variable:
	stage: test
	script:
		- echo $CI_JOB_STAGE
		- echo "$TEST" //"HELLO WORLD"
```

三、当然你也可以在gitlab的页面上创建自定义变量

1、setting > CI/CD, 展开"变量"部分
2、添加变量，填写信息

创建的变量可以像第二部一样在.gitlab-ci.yml中调用

