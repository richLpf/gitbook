### 一、前言

开发规范很重要，自动化可以节省人力，这里先简单介绍下各插件的使用，后面将会在CI中使用这些插件来做上线前的检测。

规范我们主要从几个方面下手

- 代码格式化
- StyleLint代码样式检测
- ESlint代码检测
- Git相关的内容也可以加上

下面我们就基于CRA通过各种插件的使用来保证项目规范，如何定制项目的规范，后面增加如何通过CI来检测项目

### 二、项目初始化

通过CRA新建一个项目



### 1、规范的重要性



### 2、项目环境

- mac
- node: v12.13.0
- yarn: 1.22.10

```
"devDependencies": {
    "commitizen": "^4.2.4",
    "cz-conventional-changelog": "3.3.0",
    "husky": "^7.0.4",
    "lint-staged": "^11.2.4",
    "prettier": "^2.4.1",
    "stylelint": "^13.13.1",
    "stylelint-config-standard": "^22.0.0"
},
```

这些插件一般有很多使用方式，我们这里仅配置第一种，后面根据需要补充。

1. 命令行执行格式化
2. 配合编辑器使用
3. 配置忽略文件
4. 通过配置文件修改格式规范，都可以配置多种格式包括`json`、`yml`等
5. 使用Node Api调用方法
6. 使用或开发插件
7. 配合Git Hooks使用

**代码是持续迭代的，插件的配置不同版本也有一些不同，配置的时候应当注意版本。**



## 二、引入规范

> 基本所有的规范类包，我们在上线后并不需要，所以这里都选择增加参数`-D`，例如：`yarn add [package-name] -D`

- 1、Prettier代码格式化
- 2、添加StyleLint样式检查
- 3、ESlint对JS检测告警
- 4、提交前执行单元测试
- 5、Commitizen Git提交规范并生成更新文档
- 6、Husky提交前执行命令

### 1、Prettier代码格式化

- 1) 安装依赖：`yarn add --dev --exact prettier`

- 2) 配置规则，没有的话走默认：`echo {}> .prettierrc.json`

- 3) [.prettierrc.json配置项](https://prettier.io/docs/en/options.html)
```
{ 
    "trailingComma": "es5", 
    "tabWidth": 4, 
    "semi": false, 
    "singleQuote": true 
}
```

- 4) 配置package.json使用
```
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "prettier": "prettier --parser --write ./src/**/*.js"
}
```
- 5) 执行`yarn prettier`格式化src下的代码

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/28b1fcb2598444f19d1ace0c0804abdb~tplv-k3u1fbpfcp-watermark.image?)

**用法其实很简单，最后可以通过添加`.prettierignore`文件，来忽略一些文件的格式化**

### 2、StyleLint样式检查

1) 安装依赖`yarn add --dev --exact stylelint stylelint-config-standard`

这里安装了两个包，一个StyleLint，另一个是标准的检测包。

2) 在项目根目录下新建`.stylelintrc.json`文件，自行配置规范，[配置文档](https://stylelint.io/user-guide/configure)

`.stylelintrc.json`
```
{
    "extends": "stylelint-config-standard",
    "customSyntax": "sugarss"
}
```
默认使用`stylelint-config-standard`配置，可以自行覆盖

3) 忽略某些文件检测的方法

在根目录下添加文件`.stylelintignore`或者在代码中添加如下注释
```
/* stylelint-disable */
a {}
```
4) 命令行执行，打印出检测结果

`sudo ./node_modules/.bin/stylelint "src/*.css"`

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8ca7900d592041fe9cf6f5702afa8106~tplv-k3u1fbpfcp-watermark.image?)

配置文件可以命名为：`.stylelintrc.js`、`.stylelintrc.json`、`.stylelintrc.yml`

5) 其他使用方法

在webpack中配置

```
"stylelint": {
    "extends": "stylelint-config-standard",
    "rules": {
      "color-hex-case": "lower"
    }
}
```
### 3、ESlint修改配置

一般我们使用的是CRA搭建的项目，所以ESLint是集成在项目中的，如果想要自定义配置有两种常用的方式

1) 修改package.json

```
"eslintConfig": { 
    "extends": "react-app", 
    "rules": { 
        "jsx-a11y/anchor-is-valid": 0, // 关闭a标签属性告警
        "eqeqeq": 0 // 关闭==和!= 告警
    } 
}
```
2) 执行`yarn eject`释放webpack配置，然后修改项目的ESlint规则。

3) 当然我们也可以直接安装全局`npm install -g eslint`，然后通过命令行检测代码

### 4、Commitizen

1) 安装依赖 `npm install -g commitizen`
2) 配置执行脚本
```
{
    "scripts": {
        "commit": "cz"
    }
}
```
3) 使用`cz-conventional-changelog`提交规范

`commitizen init cz-conventional-changelog --yarn --dev --exact`

在package.json中配置

```
"config": {
    "commitizen": {
      "path": "cz-conventional-changelog"
    }
}
```
或者在项目根目录下新建`.czrc`文件，并写入
```
{
  "path": "cz-conventional-changelog"
}
```
4) 执行脚本
`git add .`
`git cz`

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b6e0ba241f6a4a3392d3d45a7cd95492~tplv-k3u1fbpfcp-watermark.image?)

就会出现如下选择和输入，提交格式说明
```
1、type代表了某次提交的类型，比如修复一个bug还是新的功能上线，所有的type类型如下：
2、feat: 新增feature
3、fix: 修复bug
4、docs: 仅仅文档修改
5、style: 修改了空格，格式化等
6、refactor: 代码重构，没有添加新功能或修复bug
7、perf: 优化代码，提升代码性能，体验
8、test: 测试用例
9、chore: 改变构建流程，增加依赖库，工具等
10、revert: 回滚到上一个版本
```

5) 生成更新日志

安装依赖 `sudo cnpm install -g conventional-changelog-cli`

生成日志 `conventional-changelog -p angular -i CHANGELOG.md -s`

为了方便运行，加入package.json

```
"scripts": {
    "start": "react-scripts start",
    "prepare": "husky install",
    "prettier": "prettier --parser --write ./src/**/*.js",
    "log": "conventional-changelog -p angular -i CHANGELOG.md -s"
},
```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/293bf7917505404db463c6fd1f838b1f~tplv-k3u1fbpfcp-watermark.image?)

6) 其他常用的包

- 修改或定制提交规范`cz-customizable`
- 校验提交信息 `@commitlint/cli`

### 5、Husky

 > Git的钩子，可以在Git操作的过程中增加执行的脚本，这里我们通过Husky在提交前做一些代码的格式化或质量检查

1) 安装依赖 `yarn add husky --dev`
2) 配置package.json或者执行`yarn husky install`自动生成下面的配置

```
// package.json 
{ 
    "private": true, 
    "scripts": { 
        "postinstall": "husky install" 
    } 
}
```
3) 新建Hooks

`npx husky add .husky/pre-commit 'yarn prettier'`

`npx husky add .husky/pre-commit 'npx stylelint "**/*.css"'`

`npx husky add .husky/commit-msg 'yarn logs'`

这样，我们就添加了Hooks，具体配置信息在项目根目录下的`.husky`中。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c292cde754f942548b91d29ed24900e5~tplv-k3u1fbpfcp-watermark.image?)

4) 提交代码

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d04692e225b84a2e8f7610356aff58e4~tplv-k3u1fbpfcp-watermark.image?)

可以发现，一次提交首先执行了代码格式化，代码提交规范处理，日志的生成。

## 三、自动化规范

上面的规范都是在本地开发阶段的处理。但是开发是可以通过本地的配置忽略规范，所以在项目跑CI构建的的过程中，我们需要进行代码的的检查，根据结果执行相应的操作。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8f444d9a1aec47ea82ec75f3ecc25de7~tplv-k3u1fbpfcp-watermark.image?)

## 四、参考资料
- [Pretter官方文档](https://prettier.io/docs/en/index.html)
- [StyleLint](https://stylelint.io/)
- [commitizen文档](http://commitizen.github.io/cz-cli/)
- [Husky](https://typicode.github.io/husky/)
- [Git Hooks](https://blog.csdn.net/weixin_30693683/article/details/98416829)
- [ESLint](https://cn.eslint.org/docs/user-guide/getting-started)
- [阮一峰代码规范](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)
- https://www.cnblogs.com/jserhub/p/11924289.html
- https://www.cnblogs.com/jiaoshou/p/11284204.html