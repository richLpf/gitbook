## 一、前言

该过程中用到的技术栈`git` `gitlab` `shell`

需要提前准备的内容

- 一个项目`myweb`
- 本机安装Git
- 一个Gitlab仓库
- docker私有仓库
- gitlab runner([Gitlab-runner](http://www.baidu.com))

公司的代码一般都保存在私有化部署的Gitlab，要使用Gitlab的CI/CD，需要Gitlab版本>8.0.0

CI/CD虽然不难，但配置过程中有很多坑，而且有些要了解的概念也比较多，可以分成多个步骤，逐一攻破。

## 二、入门CI实战

### 1、安装、注册Gitlab-runner

`gitlab-runner`需要提前进行安装和注册，[详情](https://juejin.cn/post/6963927908444274718)

进入Gitlab->CICD->Runner 当前可用的runner有

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a27a99428c9a4b83abf631611ec7b8e2~tplv-k3u1fbpfcp-watermark.image)

如图所示，该项目可用的runner
- 左边runner仅可以为当前项目使用，但需要激活一下。
- 右边为共享的runner, 可以直接使用
- .gitlab-ci.yml中以tags和runner的tags关联

### 2、先把CI跑起来

首先在项目的根目录下新建`.gitlab-ci.yml`，然后在该文件中配置pipeline的任务，这些任务将会跑在`gitlab-runner`中。

一个最简单的`.gitlab-ci.yml`文件，其中CI_COMMIT_BRANCH、GITLAB_USER_LOGIN是一些gitlab定义好的变量，可以直接使用，你也可以定义自己的变量

```yml
image: "node"

stages:
  - BuildImage

before_script:
  - echo "before_script"
  - echo "This job deploys something from the $CI_COMMIT_BRANCH branch."
  - echo "Hello, $GITLAB_USER_LOGIN!"

build:
  tags:
    - test
  stage: BuildImage
  image: "node"
  script:
    - node -v
```
推送Git仓库
```
git add .gitlab-ci.yml
git commit -m "commit ci"
git push
```
进入gitlab-> CI/CD页面，可以看到一个pipeline状态是`stuck`，这是因为没有Gitlab-runner。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/67ee6a0cd779465485fbe3d9a1b3feeb~tplv-k3u1fbpfcp-watermark.image)

### 3、使用Gitlab-runner执行pipeline

修改`.gitlab-ci.yml`，仅展示部分

```
build:
  # tags,代表要使用的runner,这里改成uaek-c1 
  tags:
    - uaek-c1
  stage: BuildImage
  image: "node"
  script:
    - node -v
```
提交代码，进入CI/CD页面看到新增了一条pipeline执行完成

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2dd88eb858ee43a8956bca44c26f3844~tplv-k3u1fbpfcp-watermark.image)

点击新的记录，可以看到对应的Stage，点击当前任务

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/27eedd5a62fb4b8a8b9309d60e080ae8~tplv-k3u1fbpfcp-watermark.image)

可以看到Gitlab-runner执行`.gitlab-ci.yml`的具体信息。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/60ad7ce21a86418380401b7c120fbe79~tplv-k3u1fbpfcp-watermark.image)

到目前为止，已经看到了`.gitlab-ci.yml`触发到执行的过程，接下来，看看针对这个项目怎样去具体跑CI

## 三、项目实战配置

> 构建Docker镜像参考[详情](https://juejin.cn/post/6962037427108380709)

### 1、在项目添加Dockerfile文件和Nginx配置文件

(1)、根目录中添加配置文件`Dockerfile`

```Dockerfile
FROM node:10-alpine as builder
WORKDIR /data/myweb
COPY . .
RUN npm install --registry=https://registry.npm.taobao.org --no-package-lock --no-save
RUN yarn publish:prod

FROM nginx:alpine as myweb
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone 
WORKDIR /data/production
COPY ./nginx /etc/nginx/conf.d
COPY  --from=builder /data/myweb/build /data/production
EXPOSE 80, 443
```
(2)、在项目根目录中新建`nginx/default.conf`，我们用外挂的nginx配置文件覆盖原来Nginx镜像中的配置文件

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name localhost;

    location / {
        root /data/production;
        index index.html;
        try_files $uri /index.html;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
    	root /usr/share/nginx/html;
    }
}
```

### 2、配置文件

```yml
# 定义全局变量,镜像名称，命名空间，镜像拉取的密码的变量名
variables:
  IMAGE_HUB: "lvpf/myweb" 
  DOCKER_HUB_URL: "hub.docker.com/r/lvpf/myweb"

# 这里打印一些变量，仅仅为了展示，看下这些gitlab预设的变量值，可以去掉
before_script:
  - echo "VARIABLE CI_COMMIT_SHA IS $CI_COMMIT_SHA!"
  - echo "VARIABLE CI_COMMIT_TAG IS $CI_COMMIT_TAG!"
  - echo "VARIABLE CI_PROJECT_DIR IS $CI_PROJECT_DIR!"

# stages顺序运行, 同一个stage的所有job并行
stages:
  - BuildImage

# 任务1，构建docker镜像
docker-image-master:
  # 使用的Gitlab Runner标签
  tags:
    - uaek-c1
  # 任务名称
  stage: BuildImage
  # 由于当前runner为k8s构建的，所以这里执行docker构建和上传需要通过kaniko镜像，具体可以看下面参考文档（）
  image: gcr.io/kaniko-project/executor
  script:
    # 首先我们需要为我们的镜像生成一个tag，规则是：如果有 git tag，就使用 git tag，如果没有的话，就使用 git commit sha
    - IMAGE_TAG=$CI_COMMIT_SHA && if [[ -n "$CI_COMMIT_TAG" ]]; then IMAGE_TAG=$CI_COMMIT_TAG; fi
    - echo $IMAGE_HUB:$IMAGE_TAG
    - mkdir -p /kaniko/.docker
    - echo "{\"auths\":{\"$CI_REGISTRY\":{\"username\":\"$CI_REGISTRY_USER\",\"password\":\"$CI_REGISTRY_PASSWORD\"}}}" > /kaniko/.docker/config.json
    - /kaniko/executor --context $CI_PROJECT_DIR --dockerfile $CI_PROJECT_DIR/Dockerfile --destination $DOCKER_HUB_URL:$IMAGE_TAG
  # 使用 only 来限制这个 job 什么情况下会运行，下面的设置标识只有新的 tag 被创建时才触发CI，如果去掉，每次推送分支都会触发CI
  only:
    - tags
```

### 3、注意事项
- 一般公司安全性考虑，不会将镜像推送到hub，公司内网一般也不通，要考虑自建私有镜像仓库
- gitlab-runner可以使用的宿主机类型很多，包括云主机、docker、k8s等，构建镜像的解决方式略有不同，可以参考文档
- 其中一些涉及密码的变量，可以通过`Gitlab->Setting->CI/CD->Variables`来设置，直接在.gitlab-ci.yml中使用


## 四、参考文档

- [Gitlab CI快速开始](https://git.ucloudadmin.com/help/ci/quick_start/README)
- [在k8s构建的gitlab-runner中构建镜像-docs](https://docs.gitlab.com/ee/ci/docker/using_kaniko.html)
- [在k8s构建的gitlab-runner中构建镜像-中文](https://www.codercto.com/a/57053.html)