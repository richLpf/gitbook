## 一、前言

最近在代码评审过程中，发现了很多问题，顺便这里也对自己Review代码做一次整理。

Code Review的目的是为了规范代码、避免Bug和优化性能，进而帮助开发者成长，而不是为了追究问题或者谁对谁错。正确认识Code Review的目的，才能相互帮助，共同成长。

## 二、关注点

Code Review 要兼顾效率和质量，两者必然是矛盾的。取得平衡很重要，根据实际情况，进行Code Review。基于此，下面会整理成一份`基础的Review`和`进阶的审查Review`。

- 基础Review：是为了避免代码的混乱和Bug的产生，是开发周期内必须要考虑的点。
- 进阶Review：是为了提高编码质量，相互学习，进一步优化代码。

## 三、基础审查

- 1、编码规范
- 2、代码基本逻辑、使用体验
- 3、代码性能、组件质量

### 1、编码规范

> 规范很多，可以帮助我们提高代码的可读性，减少Bug的产生，开发中有很多常用的插件帮助我们规范代码。

- 变量命名，检查错误单词的VSCode插件，可以清楚的看到，标出拼写错误的单词（Code Spell Checker）

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5af06c3e71e047268ba1ed868d38706f~tplv-k3u1fbpfcp-watermark.image?)

- 删除非必要console、注释，帮助代码整洁
- [Prettier代码格式化](https://prettier.io/docs/en/index.html)
- [StyleLint检查样式规则](http://stylelint.docschina.org/)
- [ESlint检测告警和报错是否处理](https://eslint.bootcss.com/)
- [commitizen](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fcommitizen%2Fcz-cli "https://github.com/commitizen/cz-cli") 简单的提交规范和提交帮助工具，推荐
- [Husky代码提交前检测](https://www.npmjs.com/package/husky)

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e0594960161142d5bae5aead6a1cd09e~tplv-k3u1fbpfcp-watermark.image?)
虽然webpack打包会帮助我们清除一些无效的引入，但是这会导致代码可读性变差，越来越多的告警，屏蔽了我们对错误的查找。

上面的规则看似多，其实都是自动执行的，提交代码前会先执行Prettier，然后触发StyleLint和ESLint自动检测，通过后方可提交代码

### 2、功能验证、体验优化

> 这方面的优化主要在于开发对于产品的感觉和态度，没有更好的办法，只能一个版本一个版本的打磨和改进。

- 1）预发环境自测通过，功能是否实现

给其他同事或产品的时候，一定要通过预发环境的功能自测，开始写代码的时候往往会出现本地运行没问题，发布预发后，直接提交review或产品。

然后出现很多奇奇怪怪的问题，类似：`预发环境没有数据`，`权限不通、账号异常`，`网络不通`

- 2）兼容性测试，最简单的大小屏测试

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7cefe8eb7a674d35a529e3cd7d61957e~tplv-k3u1fbpfcp-watermark.image?)

上面的在大屏幕调试后，看起来很完美，到了小屏幕就变成了这样，这肯定是不能忍的。

**这些问题导致你写的产品，体验超级差，给人留下很不好的印象，写了还不如不写。**

- 3）站在用户的角度思考产品的使用体验，操作的易用性

最近越来越意识到产品思维对于前端的重要性。开发的产品如果使用体验差，就很难有用户使用，那么也没有存在价值。

比如下面的一个简简单单的表格展示：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9ea44489ef5c4520a302e3379f879a64~tplv-k3u1fbpfcp-watermark.image?)

`字段未翻译`
`没有筛选`
`状态不区分`
`样式丑陋`
`网络差的情况下时候loading或报错处理`

换位思考下，如果我是用户：

1. 查完数据，看着一串ID，这是啥？ 
2. 要找我想要的数据，一页一页的翻？
3. 打开页面网络差的时候，等了半分钟不知道页面在处理中。。

当你站在使用者的立场上就知道了优化的方向：**用户体验，凡是影响使用体验的地方都应该优化。**

比如用户使用表格查询数据，我们能做的优化：
- 1、查询数据：常用数据的筛选
- 2、帮助用户聚焦，隐藏一下非必要的字段
- 3、翻译字段，数据格式化
- 4、自适应屏幕大小
- 5、提供下载自定义表头等
- 6、loading、报错处理等

### 3、代码优化
- 1）封装重复代码

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1deab5c4ac1b44bcb7c5ce36c1ca9e1b~tplv-k3u1fbpfcp-watermark.image?)

比如上面两段代码，第一段是转化分钟到格式化时间，第二段是转化秒到格式化时间

完全可以通过变量进行区分入参、或者将分钟转化成秒再统一使用第二段函数调用

- 2）拆分处理多个逻辑的函数

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8d29590bf7944eda95a72e3bb1c104f9~tplv-k3u1fbpfcp-watermark.image?)

上面的函数做了两个事情，一个是处理参数，一个是请求数据，如果后面我们需要传递不同的参数或同步调用，就要重新写一遍调用函数。

类似的函数，最好能做到一个函数处理一个功能，简洁明了易于扩展。

- 3）删除冗余代码，提前结束循环

```
let arr = [0,1,2,3,4,5,6,7,8,9]
let result = 0
for(let i=0; i<arr.length; i++){
    if(i<2){
        result += arr[i]
    }
}
return result
```
类似上面的代码，当i<2的时候已经获取到结果了，但没有终止，就会多循环7次，如果数据量大的话，会导致CPU长时间的处理无用数据。

- 4）提取常量到单独文件
- 5）一些字段缺失造成页面Crash(必要时引入TS)

JS动态语言的特点，导致很容易出现读取某个对象属性的时候，属性不存在，JS报错，如果有必要`可以引入TS`或者`对必要参数做好兼容`

- 6）符合组件规范，封装是否具有可扩展性，是否易于使用。

前端大部分工作都在和组件打交道，但封装好一个组件绝不是简简单单的事，比如下面这个根据分数显示不同颜色的分数值，这确实实现了业务，但是太过业务和固化，明明可以一个对象解决的问题。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/98d2ead9273b40fd927100b4d48fc6c8~tplv-k3u1fbpfcp-watermark.image?)

```
{
    500: "#f522dd",
    200: "#faad14",
    50: "#52c41a",
    20: "#1890ff",
    10: "#1890ff"
}
```

但代码Review毕竟是有成本的，经过以上的基础审查，其实可以避免很多Bug的产生。如果有余力，也可以继续对代码进行深一层的复盘。

## 四、更多审查和优化

### 1、补充规范

上面展示了一些规范，这里还能有更多
- CSS命名可以使用短横线的方式`.font-size-12` `.float-left`
- JS变量命令：常量全部大写 `const API = "http://demo.com"`，变量可以使用下划线或者小驼峰写法`review_code` `reviewCode`
- Git分支管理规范（Git有自带的工作流管理）
- Git提交规范(插件实现)

```
<type>(<scope>):<subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```
提交格式说明
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

### 2、HTML
- 避免多余的标签嵌套
- 使用语义化标签
- 合理使用mate标签

### 3、CSS

#### 1）避免使用会导致重绘和重排的方法

- 1、添加或者删除可见的 `DOM` 元素；
- 2、元素尺寸改变——边距、填充、边框、宽度和高度
- 3、内容变化，比如用户在 `input` 框中输入文字
- 4、浏览器窗口尺寸改变——`resize` 事件发生时
- 5、计算 `offsetWidth` 和 `offsetHeight` 属性
- 6、设置 `style` 属性的值

#### 2）减少重绘重排的方法

- 1、使用 `transform` 替代 `top`
- 2、使用 `visibility` 替换 `display: none` ，因为前者只会引起重绘，后者会引发回流（改变了布局）
- 3、不要用循环的下标作为循环的key，增减元素，会导致所有节点重排
- 4、不要使用 `table` 布局，可能很小的一个小改动会造成整个 `table` 的重新布局
- 5、动画实现的速度的选择，动画速度越快，回流次数越多，也可以选择使用 `requestAnimationFrame`
- 6、CSS 选择符从右往左匹配查找，避免节点层级过多

### 4、ES6+来优化代码

> 新的语法特性可以帮助我们简化代码写法，同时提高性能，但同时要注意兼容性。

- 1、删除非必要的Debug打印内容和注释
- 2、提取常量和枚举值
- 3、多余的`if else`或简单的`switch case`，使用map映射或者三元表达式代替
- 4、箭头函数代替function函数
- 5、使用解构赋值、定义变量、读取参数等
- 6、使用Promise减少函数回调
- 7、includes
```
if(a === 1 || a === 2 || a === 3){
    return true
}
// 替换
if([1,2,3].includes(a)){
    return true
}
```
- 8、模版字符串代替+
- 9、数组去重，遍历对象
```
let arr = [...new Set([1,2,3,2,3,5])]
const data = {
    a: 1,
    b: 2
}
Object.keys(data).map(key => {
    console.log(key, data[key])
})
```

## 五、细节处理

> 俗话不是说：细节决定成败。细节真的很重要，那些优秀的作品往往都是在完成的基础上增加了许多细节。

- 1. 按钮有无节流或防抖，筛选、远程查询
- 2. 状态显示使用不同的图标，常见按钮替换成图标

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a75b48c87015458ab500bf0c8dc7e8dd~tplv-k3u1fbpfcp-watermark.image?)

下面作为对比

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/134f34a3518d4cad8ae06cdf0acb977a~tplv-k3u1fbpfcp-watermark.image?)

- 3. 增加一些切换的动画，优化体验

## 六、常用工具
- Permitter
- pre-commit
- ESLint
- 单词拼写检查【Code Spell Checker】
- 函数、代码注释规范【Add jsdoc comments】

## 七、参考资料
- https://www.cnblogs.com/jserhub/p/11924289.html
- https://juejin.cn/post/6844903897610321934#111-%E7%89%88%E6%9C%AC%E8%A7%84%E8%8C%83
- https://juejin.cn/post/6844903671457677325
- https://typicode.github.io/husky/#/?id=features