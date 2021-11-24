## 一、前言

由于浏览器同源策略，限制了页面无法引用第三方资源，无法请求不同源下面的资源，影响了web的发展，所以浏览器出让了一些安全策略来满足web的发展。

- 通过CSP安全策略来保证浏览器可以安全的加载第三方资源；
- 通过CORS允许浏览器跨域请求；
- 通过PostMessage来操作DOM

但这些策略也给了黑客攻击我们的站点的机会。

web站点常见的攻击方式包括：`XSS攻击`，`CSRF攻击`、`Sql注入攻击`等。


## 二、XSS攻击

### 1、什么是XSS攻击

XSS攻击（跨站脚本攻击），黑客通过网站的漏洞，将恶意脚本插入Html页面，当用户打开站点时，由于恶意脚本和站点代码拥有同样的执行权限，恶意脚本就有可能篡改站点内容，窃取用户信息发送到第三方服务器。

恶意代码一般会做下面这些攻击：

- 获取用户Cookies
- 修改用户DOM，伪造登录页面
- 生成浮窗广告
- 监听用户输入

### 2、XSS攻击的分类

XSS攻击常被分为三类：存储型攻击、反射型攻击、基于DOM的XSS攻击。

前两种一般要经过服务器，所以后端研发更需要注意，而最后一种没有经过服务器，是需要前端注意避免的。

#### 1）存储型攻击

一个真实的案例就是：2015年乌云网爆出来的，喜玛拉雅站点，编辑上传专辑名称，未对录入表单进行过滤，黑客可以上传恶意脚本到服务器。当其他用户点击这张专辑的时候，就执行了恶意脚本，恶意脚本获取用户的Cookies信息，上传到黑客的服务器，然后就可以登录用户的站点。

第一步：输入恶意脚本到站点，没有过滤，所以可以上传到服务器

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4f4e92677dd44714a01edea104c1b8bf~tplv-k3u1fbpfcp-watermark.image?)
第二步：当用户点击该专辑后，执行了恶意脚本，后端代码没有禁止第三方站点发出的请求，所以黑客就获取了用户的Cookies，接着就可以访问用户的站点或者调用站点的API

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fb4e440c55ed4841a70a15de04804332~tplv-k3u1fbpfcp-watermark.image?)



#### 2）反射型攻击

反射型XSS攻击的常见模式是，黑客发现了网站存在将url的参数直接在页面上显示，并且没有过滤的情况，于是黑客就构造带有恶意脚本的链接，诱导用户点击，当用户点击了链接，就将恶意代码注入到了页面并执行，从而获取用户信息等。

**反射型攻击案例：**

页面中又直接渲染html的变量，直接渲染html的，如下代码，直接读取url参数query， 在页面展示，如果后端服务没有经过过滤，就有可能将query的代码引入页面中执行，造成攻击



**![image-20211122222815674](/Users/user/Library/Application Support/typora-user-images/image-20211122222815674.png)**



![image-20211122222519524](/Users/user/Library/Application Support/typora-user-images/image-20211122222519524.png)

#### 3)基于DOM的XSS攻击

该攻击可以是一些流氓软件或攻击路由器，在http请求返回的过程中，在返回的数据中插入了恶意代码，前端站点如果没有过滤，就可能出现执行恶意代码的情况。



### 3、其他相关案例

1、页面读取html显示，后端通过抓取邮件内容，生成html，给到前端展示。由于邮件中可能存在一些非法标签，浏览器在解析中无法识别，就导致浏览器crash，常常使用插件或者js过滤，转译等。

2、给朋友发送一个页面的脚本，其中alert了6次，并且获取了浏览器Cookies

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>攻击测试</title>
    </head>
    <body>
        <script>
            window.onload = () => {
                document.cookie = "username=test"
                for(let i=0; i<=5; i++){
                    alert(`你被攻击了，我获取了你的Cookies${document.cookie}`) 
                }
            }
        </script>
        你被攻击了
    </body>
</html>
```

打开页面后：

![image-20211122220400259](/Users/user/Library/Application Support/typora-user-images/image-20211122220400259.png)

如果将其中的攻击脚本插入用户的页面中，然后向第三方服务器发送cookies，这就构成了XSS攻击



##  三、防止XSS攻击的办法

阻止XSS攻击，要先知道XSS攻击的原理：一般分为两个步骤。

第一步：黑客找到站点的漏洞，想站点插入恶意脚本。

第二步：读取站点的信息，发送到第三方服务器。



XSS攻击主要有以下方法注入：

- html中的script脚本
- 凡事可以请求第三方数据的地方：标签的属性Img的src属性、a标签的href属性、`background:url()`
- js脚本的拼接特殊字符



对于后端来说，主要有以下几处常常不可信：

- 用户输入内容
- url参数
- post参数
- 第三方的链接
- referere
- Cookies



### 1、过滤关键字

对于存储型XSS攻击和反射型XSS攻击，二者由于都需要过服务器，那么只要后端开发在入库的时候最敏感代码进行过滤进行过滤，就可以避免大部分的XSS攻击。

### 2、前端框架和组件的使用

目前对于前端的大部分组件（富文本组件）和常见的Vue、React框架都对展示的数据，只会展示以字符串进行展示。

对于有可能嵌入恶意代码的部分也贴心的增加了提示：

如在Vue中：

```vue
<div v-html="html"></div>
```

在React中：

```react
<div dangerouslySetInnerHTML = {{__html:<div><script src="http://xxs.js"></script></div>}} ></div>
```

这些可能导致XSS攻击的地方，框架都给了显著的提示，所以在页面显示这些不可控的内容前要进行过滤。

### 3、严格执行CSP策略

设置CSP，现在站点加载或发送请求到第三方服务器

### 4、给Cookie设置HttpOnly

给cookies设置了该属性，当前页面重要的Cookie就无法通过js读取，这样黑客也就无法获取用户的信息