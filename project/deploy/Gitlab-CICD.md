## Gitlab CICD自动构建- .gitlab-ci.yml详细配置解读

这是我参与更文挑战的第3天，活动详情查看： [更文挑战](https://juejin.cn/post/6967194882926444557)

## 一、前言

> `.gitlab-ci.yml`有很多参数，如果第一次接触肯定很晕，这里常用的配置再一次进行解读，对一些概念增加具体的展示，从而更好的理解。

`.gitlab-ci.yml`有以下几种能力
- 1、运行的shell脚本，通过script文件
- 2、引用的其他模版文件和配置文件，通过include指令
- 3、定义缓存和依赖，通过cache指令
- 4、定义要执行的任务和执行的顺序，pipeline、jobs
- 5、应用部署的位置，自定定义
- 6、启动CI的方式，API和UI

## 二、基础概念

### 1、Pipelines

当配置好`.gitlab-ci.yml`后，可以触发CI，

在`Gitlab -> CICD`可以看到如图所示，启动的`Pipeline`，

成功的现实`passed`, 失败的显示`failed`，

每个pipeline都是有一个个Job构成的，在`Gitlab-runner`中执行

![企业微信截图_8b01271f-afe2-4aca-b9a4-e69b9e5f2a7f.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f6f9427f4dcc426f8d99e4f13e0b839a~tplv-k3u1fbpfcp-watermark.image)

### 2、Jobs

点击上图的pipeline，可以看到下图，里面包括了两个Jobs，这些Jobs执行顺序可以在`.gitlab-ci.yml`中配置，也可以配置并行执行。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0a7fe9ab583846f7941a268bcb544293~tplv-k3u1fbpfcp-watermark.image)

### 3、Variables

（1）在`.gitlab-ci.yml`中定义变量，Jobs中定义的变量有作用域。
![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/67a8113807d440b18c8b10d2cf6a71c9~tplv-k3u1fbpfcp-watermark.image)

（2）官方预定义变量，[参考](https://docs.gitlab.com/ee/ci/variables/predefined_variables.html)

（3）在Gitlab->CICD->Variables中定义

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fc2f117e76de488c8bdfcfc7d01a1931~tplv-k3u1fbpfcp-watermark.image)

以上变量在`.gitlab-ci.yml`都可以直接使用

### 4、Runners

Runner 使用来用执行Pipeline的应用，需要提前安装注册，具体详情可以参考我的另一篇文章[Gitlab CI/CD执行流程和Gitlab Runner安装和注册](https://juejin.cn/post/6963927908444274718)

## 三、示例文件

下面是一份配置文件示例，接着来详细解释一下这些关键字的意义

```
include:
  - remote: "http://www.baidu.com/data/prod.yml"

variables:
  - buildImage: "http://hub.com/nginx"

workflow:
  rules:
    - if: '$CI-PIPELINE_SOURCE' == "push"'
      when: never
    - when: always

stages:
    - build
    - nextStep
    - testStep1

build-job:
  stage: build
  script:
    - echo "Hello, $GITLAB_USER_LOGIN!"

test-job1:
  stage: nextStep
  script:
    - echo "This job tests something"

test-job2:
  stage: nextStep
  script:
    - echo "This job tests something, but takes more time than test-job1."
    - echo "After the echo commands complete, it runs the sleep command for 20 seconds"

deploy-prod:
  stage: testStep1
  script:
    - echo "This job deploys something from the $CI_COMMIT_BRANCH branch."
```
这份文件包含了四个可执行的Jobs，通过stages定义执行的顺序，test-job1、test-job2的stage名字相同都是nextStep, 此时这两个任务将并列执行。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cfd0784709ef4d8fa8d76d59b495bb0b~tplv-k3u1fbpfcp-watermark.image)

## 四、全局关键字

`.gitlab-ci.yml`可以分为几个层级，首先最外层的变量包括：

| 关键字    | 说明                    | 值                                       | 备注                                                         |
| --------- | ----------------------- | ---------------------------------------- | ------------------------------------------------------------ |
| stages    | 阶段，类型数组          | 分别为自定义的jobs                       | 规定各个任务的执行顺序，任务名称相同，则同时执行             |
| include   | 应用的yml或yaml配置文件 | key: 包括，local、remote、file、template | local 一般是本地文件，remote 可以是远程其他可访问的地址，filter一般是其他项目下的文件路径，template是官方提供的模版 |
| variables | 变量                    | 预定义或自定义                           | 根据变量位置不同，优先级不样，相同的变量会根据优先级进行覆盖 |
| workflow  | 工作流                  | rules                                    | 用来定义CI/CD何时触发，和jobs中的rules、only相似             |

### 1、variable

变量有很多种情况，包括
- 预定义
- 自定义声明（全局变量或者在job、scripts中声明）
- 在Gitlab->setting->CICD中添加，一般用来保存账号秘密

变量使用的代码示例，展示变量的使用，打印信息

```
before_script:
  - echo "this is .gitlab-ci.yml $CI_PIPELINE_SOURCE $CI_COMMIT_TAG, $CI_COMMIT_BRANCH"
  - echo "VARIABLE CI_COMMIT_SHA IS $CI_COMMIT_SHA!"
  - echo "VARIABLE CI_COMMIT_TAG IS $CI_COMMIT_TAG!"
  - echo "VARIABLE CI_PROJECT_DIR IS $CI_PROJECT_DIR!"
```

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/027974a441f14dfab823a7f4516d1209~tplv-k3u1fbpfcp-watermark.image)

**变量使用的注意事项：**
- 变量有优先级，如果相同会被覆盖
- 变量定义的地方很多，包括全局，jobs中，scripts、Gitlab项目或组的变量页面（页面常常用来配置安全性较高的密钥），都可以在配置文件中直接使用
- Gitlab每次迭代会增加不同的变量，在使用一些不常用的变量时，要注意当前Gitlab版本是否支持
- 变量在scripts中使用:`$CI_BUILDS_DIR`
- [预定义变量](https://docs.gitlab.com/ee/ci/variables/predefined_variables.html)

### 2、jobs和stages

- 像上面的示例配置，配置了`build-job`、`test-job1`、`test-job2`、`deploy-prod`四个要执行的任务。
- stages 定义执行的顺序，其中stage为`nextStep`的任务并行执行
- 要求最少配置一个任务。
- 每个Job都是pipeline的一部分。
- 这些Job将会在Giblab Runner中执行
- 这些Job具体的执行过程将会显示在控制台，每个阶段的状态都会实时更新。

具体的执行展示

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7042036e0351435996820f4b5e49fdb6~tplv-k3u1fbpfcp-watermark.image)

> stages 就是用来确定jobs的执行顺序

### 3、include

include指令常用的属性包括：`local`、`remote`、`file`、`template`，写法很多，是用来引入额外的yml配置文件的，并进行深度合并，有以下特点：
- include在一个文件可以出现多次
- 不管include写在文件的哪个地方，都会首先引入，并合并
- include引入的文件如果变量相同会进行覆盖，不会多次执行。
- include的写法很灵活，后面是loacl和remote可以直接省略。
- include可以使用定义好的变量。
- local 表示引入当前项目的文件。
- remote yml文件的仓库必须是public的，不然就报错，这就代表着可以将yml文件单独存放在一个仓库，通过引用获取
- project 表示引用当前用户可访问的项目的yml文件
- template 表示可以使用Gitlab定义好的yml文件，[官方的公用模版](https://gitlab.com/gitlab-org/gitlab/tree/master/lib/gitlab/ci/templates)

include后可以简写，直接引入remote地址，或者写local文件，常见的写法有：

```
include: "http://www.baidu.com/file/prod.yml"
include: ".test1.yml"
include:
  - local: ".test1.yml"
  - remote: "http://www.baidu.com/file/prod.yml"
include: 
  - "http://www.baidu.com/file/prod1.yml"
  - ".test.yml"
  - template: "Auto-DevOps.gitlab-ci.yml"
include:
  - project: "console/gitlab-ci-templates"
    ref: "master"
    file: ".gitlab-ci-console-cli.yml"
```

### 4、workflow

`workflow`决定何时触发pipeline或者禁止pipeline

主要参数为：
- if结合预定义变量判断条件为真，则触发pipeline或禁止
- 满足if条件，可以直接定义变量

```
workflow:
  rules: 
    - if: '$CI_COMMIT_SOURCE = "schedule"'
      when: never
    - if: '$CI_COMMIT_SOURCE = "push"'
      when: never
    - when: always
    - if: $CI_COMMIT_BRANCH = "feature"
      variables:
        IS_A_FEATURE: "true" 
```
如上示意，满足if条件的情况下，看when的值，never表示在这种情况下禁止pipeline，不写则默认满足情况下，触发pipeline。最后配置when: always，表示除了上面满足条件的，并且when值为never的，都会触发。

最后一个`$CI_COMMIT_BRANCH = feature` 分支名为feature的时候定义变量`IS_A_FEATURE`

用法类似：Jobs中的rules和only

## 五、Jobs的关键字

- 每个项目的`.gitlab-ci.yml`中至少有一个Job
- 每个项目通过定义配置字段实现不同的功能，如单元测试，静态检查，构建镜像等
- 一个任务中，除了`scripts`，其他都是非必需的
- 这些变量很多可以直接声明在全局中，如果任务中有重名，会进行覆盖

| 关键字        | 说明                                                         | 备注                                                         |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| image         | gitlab-runner 执行需要的docker镜像                           |                                                              |
| services      | 执行阶段同步依赖的docker服务                                 |                                                              |
| script        | docker中执行的语句                                           | before_script, after_script, script 声明一个个脚本的数组，依次执行 |
| stage         | 用来声明执行哪个任务，放在stages数组中，如果两个相同，同步执行 | 如果不定义，stages将按.pre  build test  deploy .post 顺序执行 |
| extends       | 定义公用配置，通过extends字段重复引用                        | 数组引入，相同层级的配置，后面会覆盖前面的，可以和include一起使用 |
| rules         | 同when类似，在任务级别定义执行的时间，当判断条件为true时，触发CI | 可判断的字段很多，可以根据需求组合                           |
| only, except  | only、except同rules类似，用来定义CI的触法时机                | 建议使用rules配置                                            |
| tags          | 定义执行该Job的runner，值为定义runner的tag，不写则无法执行   |                                                              |
| allow_failure | 允许当前任务失败，不会中断其他任务的执行                     | 还可以定义具体遇到哪些错误code码，继续执行                   |
| when          | 定义什么时候执行job, 默认值on_success，表示当前任务之前的任务全部执行成功，则执行当前工作 | 其他的常见属性有：on_failure,always,manual,delayed,never     |
| cache         | 定义缓存文件或缓存文件夹                                     |                                                              |
| environment   | 定义Job工作的环境变量 ｜                                     |                                                              |
| artifacts     | 可以在任务执行完毕后，缓存对应的资源，可以直接下载本地       |                                                              |
| coverage      | 设置代码测试覆盖率                                           |                                                              |
| retry         | 设置Job执行失败后的重试次数                                  |                                                              |
| timeout       | Job执行的超时时间                                            |                                                              |
| parallel      | 配置当前并行的任务数量 范围：2-50个                          |                                                              |
| trigger       | 执行任务的时候，触发其他项目的CI流程                         |                                                              |
| variables     | Jobs维度的变量                                               |                                                              |

### 注意：不能用作job的变量有

`image`
`services`
`stages`
`types`
`before_script`
`after_script`
`variables`
`cache`
`include`

由于这些变量有了特定的意义，所以不能再作为Job的名字使用。

## 六、Jobs关键词解读

### 1、docker、services

docker 当前Job要使用的镜像，如果当前Job没有声明，那么将会使用全局定义的docker。

**Docker属性**
- name 名称，如果只有一个名字，可以省略
- entrypoint docker拉取后执行的命令

**services属性**
- alias 服务别名
- name 名称，如果只有一个名字，可以省略
- entrypoint 拉取镜像启动容器后，要执行的命令
- command 要执行的命令

```
image:
  name: ruby:2.6
  entrypoint: ["/bin/bash"]

services:
  - name: my-postgres:11.7
    alias: db-postgres
    entrypoint: ["/usr/local/bin/db-postgres"]
    command: ["start"]
```

### 2、script

script是Job的必选字段，用来写shell脚本，可以执行多个行命令

`before_script`、`after_script`分别在Job开始之前前和结束后执行的脚本，可以写在全局变量中，如下图：

```
job:
  before_script:
    - echo "Execute this script instead of the global before_script."
  script:
    - echo "This script executes after the job's `before_script`"
```

### 3、environment

```
testStep1:
  tags:
    - uaek-c1
  stage: nextStep
  image: nginx
  script:
    - echo "this is third testStep"
  environment:
    name: dev
    url: http://www.baidu.com
```

增加`environment`字段，将会在Gitlab页面看到下面的变化

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ec85f26590f4bc6ba2bea37f8d4cab2~tplv-k3u1fbpfcp-watermark.image)
1、CI/CD，进入某个任务页面，上面将会显示`environment`。


![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ee3d28248de24f3ca4c1a681e5df8125~tplv-k3u1fbpfcp-watermark.image)
2、`Operations`这里显示的是，Job按`environment`进行分类，点击dev打开下面页面

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/82c86702e042469f9106a60d3d985e5c~tplv-k3u1fbpfcp-watermark.image)

3、可以看到`View deployment`的按钮,点击将会跳转到`environment`字段下设置的url，上面的例子会跳转到`http://www.baidu.com`,这样可以在发布成功后，方便进行回归

### 4、extends

extends 可以引用定义的公用配置，同时引入多个，将进行合并

如果相同层级的变量相同，后面变量的会被前面的覆盖。

```yml
.only-important:
  variables:
    URL: "http://my-url.internal"
    IMPORTANT_VAR: "the details"
  only:
    - main
    - stable
  tags:
    - production
  script:
    - echo "Hello world!"

.in-docker:
  variables:
    URL: "http://docker-url.internal"
  tags:
    - docker
  image: alpine

rspec:
  variables:
    GITLAB: "is-awesome"
  extends:
    - .only-important
    - .in-docker
  script:
    - rake rspec
```
上面的配置文件实际合并后

```
rspec:
  variables:
    URL: "http://docker-url.internal"
    IMPORTANT_VAR: "the details"
    GITLAB: "is-awesome"
  only:
    - main
    - stable
  tags:
    - docker
  image: alpine
  script:
    - rake rspec
```

### 5、rules

rules 的属性有
- if 条件为true触发
- when 默认`on_success`, 其他值`delayed`, `start_in`
- allow_failure Job执行失败，是否继续pipeline

```
docker build:
  script: docker build -t my-image:$CI_COMMIT_REF_SLUG .
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
      when: delayed
      start_in: '3 hours'
      allow_failure: true
```

| 触发条件                    | 说明                                          |
| --------------------------- | --------------------------------------------- |
| branches                    | 当分支被push                                  |
| API                         | 通过API触发pipeline                           |
| external                    | 当使用CI第三方服务                            |
| external_pull_request_event | 通过外部仓库拉取请求                          |
| merge_request_event         | 合并git请求的时候，触发pipeline               |
| pipeline                    | 使用项目中trigger字段，触发要执行的CI的项目   |
| push                        | 分支推送或者tag推送                           |
| schedule                    | 在gitlab->schedule 页面配置定时执行pipeline   |
| trigger                     | 通过token触发                                 |
| web                         | 在Gitlab上pipeline页面，点击run pipline的时候 |
| ｜webide                    | 使用WebIDE 触发CICD                           |

rules像when一样，可以灵活配置对应的触发规则，也可以用来替换only

```
job:
  script: echo "Hello, Rules!"
  rules:
    - if: '$CI_PIPELINE_SOURCE == "schedule"'
      when: manual
      allow_failure: true
    - if: '$CI_PIPELINE_SOURCE == "push"'
```

### 6、only, except

only 仅当符合条件的时候触发CI，这里可以写对应的标签，也可以是分支等
except 正好相反，符合条件不会触发CI

- variables 利用这两个参数定义变量，作用范围只能是当前Job
- refs 后面是对应的分支名称，可以使用正则来匹配
- changes 会监听文件的变化，来触发CI

```
deploy:
  script: cap staging deploy
  only:
    variables:
      - $RELEASE == "staging"
    refs:
      - main
      - /^issue-.*$/
      - merge_requests
    changes:
      - Dockerfile
```

```
job2:
  script: echo 'test'
  only:
  - branches
  - tags
```

### 7、tags

```
job:
  tags:
    - uaek-c1
```

tags的值为创建Gitlab runner的标签，而配置这个值，代表当前Job将会在对应标签的Runner中执行。

### 8、cache、artifacts

cache缓存文件或文件夹，加快构建速度

artifacts 配置文件或文件夹，配置后，当任务执行成功后可以在Gitlab UI进行下载，如图所示

```
  tags:
    - uaek-c1
  stage: nextStep
  parallel: 2
  image: nginx
  artifacts:
    paths:
      - package.json
  script:
    - echo "this is first nextStep"
```

![企业微信截图_2ceca07f-f03d-4faf-b7ed-2954c2e68691.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/65267b44108441c589bf66c69f6bf78d~tplv-k3u1fbpfcp-watermark.image)